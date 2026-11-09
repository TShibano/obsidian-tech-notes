---
title: "OpenID Connect"
date: 2026-11-09
tags:
  - Security
  - Auth
  - Web
  - API
related:
  - "[[SAML]]"
  - "[[SSO]]"
  - "[[LDAP]]"
  - "[[Active Directory]]"
  - "[[ゼロトラストセキュリティ]]"
  - "[[REST API]]"
  - "[[HTTP・HTTPS]]"
  - "[[JSON]]"
  - "[[OAuth 2.0]]"
  - "[[SAP]]"
---

## 概要

OpenID Connect（OIDC）は，OAuth 2.0 の上に載る「シンプルなアイデンティティレイヤ」であり，認可サーバー（OP）による認証結果に基づいてクライアント（RP）がエンドユーザーの身元を検証し，基本的なプロフィール情報を取得できるようにする仕様である．OAuth 2.0 が「認可（アクセス委譲）」のための枠組みであるのに対し，OIDC は「認証（誰がログインしたか）」を標準化し，結果を署名付き JWT の ID トークンとして返す．

## 詳細

### OAuth 2.0 との関係

- OAuth 2.0 はアクセストークンで API へのアクセス権を委譲するが，ユーザーが誰かは定義しない．OIDC は `openid` スコープを指定することで OAuth 2.0 の認可リクエストを認証リクエストに拡張し，ID トークンと UserInfo エンドポイントを追加する
- 役割の呼び方: OAuth のリソースオーナー / クライアント / 認可サーバーに対し，OIDC ではエンドユーザー / RP（Relying Party）/ OP（OpenID Provider）を用いる
- アクセストークンは API 向け（audience は API），ID トークンは RP 向け（audience は `client_id`）であり，ID トークンを API 呼び出しに使ってはならない

### ID トークン（JWT）

ID トークンは認証イベントに関するクレームを含む署名付き JWT．Core 仕様で必須とされるクレームは次のとおり．

| クレーム | 意味 |
|----------|------|
| `iss` | 発行者識別子（https の URL） |
| `sub` | ユーザーの一意識別子．発行者内で再割り当てされず，最大 255 ASCII 文字 |
| `aud` | 受信者．RP の `client_id` を含む |
| `exp` | 有効期限 |
| `iat` | 発行時刻 |
| `nonce` | 認可リクエストに `nonce` を含めた場合は同値を必ず返し，RP が検証する |

主な検証手順（Core 3.1.3.7）: 暗号化されていれば復号 → `iss` が OP の発行者識別子と完全一致 → `aud` に自分の `client_id` が含まれ，信頼しない audience がない → 署名を OP の鍵で検証 → `exp` を過ぎていない → `iat` で古すぎるトークンを制限 → `nonce` の一致とリプレイ確認 → 要求した場合は `acr` / `auth_time` を確認．

### 各フロー

| フロー | response_type | トークンの取得元 | 現状の位置づけ |
|--------|---------------|------------------|----------------|
| 認可コードフロー | `code` | すべて Token エンドポイント | 推奨．PKCE と併用 |
| インプリシットフロー | `id_token` / `id_token token` | すべて認可エンドポイント | OAuth 2.0 Security BCP は使わないよう推奨 |
| ハイブリッドフロー | `code id_token` 等 | 認可コードは認可エンドポイント，他は両方 | 特殊用途 |

認可コード + PKCE の流れ:

1. RP が `code_verifier` を生成し，`code_challenge`（S256）を計算する．あわせて `state` と `nonce` を生成
2. ブラウザを OP の認可エンドポイントへリダイレクト（`scope=openid profile email`，`code_challenge` 等を付与）
3. OP がユーザーを認証・同意取得し，`code` と `state` を付けて `redirect_uri` に戻す
4. RP がバックチャネルで Token エンドポイントに `code` と `code_verifier` を送る
5. OP が ID トークン，アクセストークン（任意でリフレッシュトークン）を返す．RP は ID トークンを検証し，必要なら UserInfo エンドポイントでクレームを追加取得する

PKCE（RFC 7636）は認可コード横取り攻撃への対策で，OAuth 2.0 Security BCP（RFC 9700）はパブリッククライアントに必須（MUST），認可サーバーには対応を必須とし，機密クライアントにも推奨している．

### Discovery と JWKS

- OP は `{issuer}/.well-known/openid-configuration` にメタデータ JSON を公開する．RP はエンドポイントをハードコードせずここから取得する
- 必須項目: `issuer`，`authorization_endpoint`，`jwks_uri`，`response_types_supported`，`subject_types_supported`，`id_token_signing_alg_values_supported`（`token_endpoint` なども実質必要）
- 返された `issuer` は取得に使った Issuer URL と同一で，ID トークンの `iss` とも一致しなければならない（完全一致）
- `jwks_uri` は OP の署名用公開鍵（JWK Set）の URL．ID トークンヘッダの `kid` で鍵を選び，鍵ローテーションに備えてキャッシュと再取得を実装する
- アルゴリズムは許可リストで固定し，`alg: none` を受け入れない（RFC 8725 参照）

### SAML との比較

| 観点 | OIDC | SAML 2.0 |
|------|------|----------|
| ベース | OAuth 2.0 + JWT（JSON） | XML アサーション + XML 署名 |
| 策定 | OpenID Foundation．Core 1.0 は 2014 年 2 月 | OASIS．2005 年 3 月 |
| 主な用途 | モバイル，SPA，API，コンシューマー向け | 企業向け Web SSO，B2B フェデレーション |
| 実装負荷 | 比較的軽い | XML 処理が重く，実装が複雑 |
| API 連携 | アクセストークンと自然に統合 | 別途トークン変換が必要 |

どちらも Web SSO を実現するが，新規開発や API 中心の構成では OIDC が選ばれやすく，既存の企業 IdP（[[Active Directory]] FS など）や SaaS との連携では SAML が残る．両方を話せる IdP を置き，アプリ側は OIDC に寄せる構成が一般的．詳細は [[SAML]] を参照．

### 最新動向（2026-11-09 時点）

- **仕様の標準化**: OIDC Core / Discovery / RP-Initiated Logout / Session Management / Front-Channel Logout / Back-Channel Logout は Final で，ISO/IEC 26131〜26137:2024 として国際標準化されている．Core は errata set 2（2023-12-15）を反映
- **OAuth 2.1**: IETF の Internet-Draft（draft-ietf-oauth-v2-1，取得時点で -17，2026-10-30 更新）で，RFC 6749 / 6750 を置き換える予定．PKCE 必須化，インプリシットとパスワードグラントの削除，リダイレクト URI の完全一致（ループバックのポートのみ例外）が主な変更．ワーキンググループのマイルストーンは 2026 年 12 月の IESG 提出であり，まだ RFC ではない
- **OAuth 2.0 Security BCP（RFC 9700, BCP 240）**: インプリシット非推奨，完全一致のリダイレクト URI，送信者制約トークン（mTLS: RFC 8705 / DPoP: RFC 9449），マルチ認可サーバー時のミックスアップ対策として RFC 9207 の `iss` レスポンスパラメータ
- **FAPI 2.0 Security Profile**: 2025-02-22 に Final．PAR（Pushed Authorization Request）必須，PKCE（S256）必須，送信者制約アクセストークン（mTLS または DPoP）のみ発行，クライアント認証は mTLS または `private_key_jwt`，署名は PS256 / ES256 / EdDSA を推奨し `none` は禁止．金融・医療など高保証領域向け
- **周辺仕様**: OpenID Federation 1.0（Final），OpenID Connect for Identity Assurance 1.0（Final），OpenID for Verifiable Presentations 1.0（Final），HAIP（Final）など，OIDC を軸にした仕様群が拡大している

### 実装上の注意

- `state`（CSRF 対策）と `nonce`（ID トークンのリプレイ対策）を必ず使い，検証する．一部のライブラリは `nonce` を自動検証しないので明示的に比較する
- リダイレクト URI は事前登録して完全一致で照合する
- 自前実装せず，実績のある OIDC ライブラリや IdP（Keycloak，Auth0，Entra ID，Google など）を使う
- アクセストークンは短寿命（数分〜十数分）にし，リフレッシュトークンはローテーションと再利用検知を行う

## ポイント

- OIDC = OAuth 2.0 + 認証．`openid` スコープと ID トークン（JWT）が核
- 新規実装は認可コードフロー + PKCE が基本．インプリシットは避ける
- ID トークンは `iss` / `aud` / `exp` / `nonce` / 署名を必ず検証し，API には使わない
- Discovery（`/.well-known/openid-configuration`）と JWKS で設定と鍵を動的に取得する
- OAuth 2.1 はまだドラフトだが，PKCE 必須などは RFC 9700 で既にベストプラクティス化している
- 高保証が必要なら FAPI 2.0（PAR + PKCE + DPoP / mTLS）を検討する

## 関連項目

- [[SAML]]
- [[SSO]]
- [[LDAP]]
- [[Active Directory]]
- [[ゼロトラストセキュリティ]]
- [[REST API]]
- [[HTTP・HTTPS]]
- [[JSON]]
- [[OAuth 2.0]]（ファントムリンク）
- [[SAP]]

## 参考

- [OpenID Connect Core 1.0 incorporating errata set 2](https://openid.net/specs/openid-connect-core-1_0.html)
- [OpenID Connect Discovery 1.0](https://openid.net/specs/openid-connect-discovery-1_0.html)
- [OpenID Foundation Specifications](https://openid.net/developers/specs/)
- [FAPI 2.0 Security Profile](https://openid.net/specs/fapi-security-profile-2_0-final.html)
- [The OAuth 2.1 Authorization Framework (draft-ietf-oauth-v2-1)](https://datatracker.ietf.org/doc/draft-ietf-oauth-v2-1/)
- [RFC 9700: Best Current Practice for OAuth 2.0 Security](https://www.rfc-editor.org/rfc/rfc9700)
