---
title: "SAML"
date: 2026-11-09
tags:
  - Security
  - Auth
  - Web
  - API
related:
  - "[[SSO]]"
  - "[[OpenID Connect]]"
  - "[[LDAP]]"
  - "[[Active Directory]]"
  - "[[XML]]"
  - "[[ゼロトラストセキュリティ]]"
  - "[[電子証明書]]"
  - "[[SAP]]"
---

## 概要

SAML（Security Assertion Markup Language）は，IdP（Identity Provider）と SP（Service Provider）の間で認証・属性・認可の情報を XML のアサーションとして受け渡すための OASIS 標準のフェデレーション規格．主に企業向け [[SSO]] で使われ，現行は SAML 2.0（2005年3月に OASIS Standard）．

## 詳細

### 歴史とバージョン

- SAML 1.0 は2002年11月，1.1 は2003年9月に OASIS Standard となった
- SAML 2.0 は2005年3月に OASIS Standard となり，Liberty Alliance の ID-FF と Shibboleth の拡張を統合した．SAML 1.1 とは互換性がない

### 登場人物

- Principal: 通常はユーザー
- IdP: 認証を行い，アサーションを発行する側（Entra ID，Okta など）
- SP: アサーションを受け取り，アクセス制御に使う側（SaaS アプリなど）

### アサーション

主体について発行者が真と主張する情報を運ぶ．文（statement）は3種類．

| 種類 | 内容 |
|------|------|
| Authentication statement | 主体がいつ，どの方法で IdP に認証したか |
| Attribute statement | 主体に紐づく名前と値の属性（メール，グループなど） |
| Authorization decision statement | 主体がリソースに対して操作できるか（機能は限定的．高度な用途は XACML） |

### プロトコルとバインディング

バインディングは SAML メッセージを下位プロトコルでどう運ぶかを定める．SAML 2.0 の主なものは次のとおり．

| バインディング | 概要 |
|----------------|------|
| HTTP Redirect | URL パラメータでメッセージを運ぶ．AuthnRequest によく使う |
| HTTP POST | HTML フォームの自動送信で運ぶ．署名付き Response によく使う |
| HTTP Artifact | 参照（artifact）だけをブラウザ経由で渡し，実体は SOAP で直接取得する |
| SOAP / PAOS | バックチャネル通信，Enhanced Client 用 |
| URI | URI 解決によるアサーション取得 |

### Web Browser SSO プロファイル

- **SP-initiated**: ユーザーが SP の保護リソースにアクセス → SP が AuthnRequest を作り，ブラウザ経由で IdP に送る（多くは Redirect）→ IdP が認証 → IdP が署名付き Response（アサーション入り）を POST で SP の ACS（Assertion Consumer Service）へ返す．RelayState で元の URL などを引き継ぐ
- **IdP-initiated**: ユーザーが IdP で認証済みの状態から SP のタイルをクリック → IdP が AuthnRequest なしで Response を SP の ACS に送る
- ほかに Single Logout，Name Identifier Management，IdP Discovery，Enhanced Client or Proxy などのプロファイルがある

### メタデータ

SAML エンティティ間で設定情報を共有する XML．エンドポイント（SSO URL，ACS URL），対応バインディング，署名・暗号化用の公開鍵（証明書）などを記述する．IdP と SP が互いのメタデータを取り込むことで信頼関係を設定する．証明書の更新時はメタデータの再配布が必要になる（[[電子証明書]]）．

### 署名と暗号化

- ブラウザ経由で配送される Response は XML Signature で署名することが必須とされる（技術概要 4.6 節）．POST で送るアサーションも署名必須
- 仕様はトランスポートに TLS，メッセージレベルに XML Signature / XML Encryption を推奨している

### 主な脆弱性

SAML の実装は XML の解釈に強く依存するため，署名検証まわりの実装バグが繰り返し見つかる．

- **XML 署名ラッピング（XSW）**: 署名済み要素を残したまま，偽のアサーションを別の位置に挿入する攻撃．署名検証を行う箇所と，属性を読み出す箇所が別の要素を見ていると成立する
- **パーサ差異**: 署名検証と値の取得で異なる XML パーサを使うと，同じ文書の解釈が食い違う．PortSwigger の「SAML roulette」（2025-03-18）では ruby-saml が REXML で署名検証し Nokogiri で属性を読む点を突き，名前空間の混乱などで署名検証を回避し，GitLab Enterprise で認証なしに管理者権限を取得する攻撃チェーンを示した．ruby-saml の CVE-2025-25291 は同種の署名ラッピングで，1.12.4 / 1.18.0 で修正された
- **xml-crypto（SAMLStorm）**: Node.js の xml-crypto に DigestValue 内のコメントを使った署名検証バイパス（CVE-2025-29775）があり，v6.0.1 で修正（v3.2.1，v2.1.6 にバックポート）
- 検証時の基本的な確認項目: 署名の対象が使用するアサーションと一致するか，Issuer，Audience，Recipient，NotOnOrAfter，InResponseTo，リプレイ防止（ID の一回限り使用）

### OpenID Connect との比較

| 観点 | SAML 2.0 | [[OpenID Connect]] |
|------|----------|-------------------|
| メッセージ形式 | XML | JSON（JWT） |
| 基盤 | 独自のプロトコル体系 | OAuth 2.0 の上の認証層 |
| 実装の難度 | XML 処理が必要で高め | REST / JSON で低め |
| モバイル / SPA | 不向き | ネイティブに対応 |
| 位置づけ | 成熟した企業向けフェデレーション | 新規開発・クラウドネイティブ向け |

Microsoft Entra のガイドは，新規 SaaS では OIDC を既定の選択とし，SAML は「SAML を要求する企業顧客」「SAML のみ対応の旧 IdP」「調達・コンプライアンスで SAML が必須」の場合に選ぶとしている．顧客層が混在するなら両方に対応する．

### SSO での使われ方

- 企業が IdP（Entra ID，Okta など）を一元的な認証基盤とし，各 SaaS を SP として SAML 連携する．[[Active Directory]] や [[LDAP]] のディレクトリが IdP の背後にあることが多い
- SaaS ベンダーは，顧客ごとの IdP 設定（メタデータ交換，属性マッピング）を受け付ける形で「Enterprise SSO」を提供する
- IdP ダッシュボードのタイルから起動する IdP-initiated の要望は多いが，リクエストと対応づける InResponseTo がなく，リプレイやログイン CSRF のリスクが高い．セキュリティ重視のアプリでは SP-initiated を推奨し，IdP-initiated は必要に応じて限定的に許可するのが一般的（ベンダー記事ベース）

## ポイント

- SAML = XML アサーションによるフェデレーション．IdP が認証し，SP が信頼してログインさせる
- 実運用の中心は Web Browser SSO（Redirect + POST）
- セキュリティ上の最大の論点は XML 署名検証の実装．自前実装を避け，メンテされたライブラリを使い，常に最新化する．署名検証と値取得で同じパーサ・同じ要素を使う
- IdP-initiated は SP-initiated より弱い．本当に必要な場合に限る
- 新規は OIDC，既存の企業連携や要件で必要な場合は SAML，両対応も現実的
- メタデータと証明書の有効期限管理が運用上の落とし穴になる

## 関連項目

- [[SSO]]
- [[OpenID Connect]]
- [[LDAP]]
- [[Active Directory]]
- [[XML]]
- [[ゼロトラストセキュリティ]]
- [[電子証明書]]
- [[SAP]]

## 参考

- [Security Assertion Markup Language (Wikipedia)](https://en.wikipedia.org/wiki/Security_Assertion_Markup_Language)
- [SAML V2.0 Technical Overview (OASIS)](https://docs.oasis-open.org/security/saml/Post2.0/sstc-saml-tech-overview-2.0.html)
- [SAML roulette: the hacker always wins (PortSwigger Research)](https://portswigger.net/research/saml-roulette-the-hacker-always-wins)
- [CVE-2025-25291 (NVD)](https://nvd.nist.gov/vuln/detail/CVE-2025-25291)
- [SAMLStorm (WorkOS)](https://workos.com/blog/samlstorm)
- [CVE-2025-29775 (NVD)](https://nvd.nist.gov/vuln/detail/CVE-2025-29775)
- [SAML versus OpenID Connect: Choose the right SSO protocol (Microsoft Learn)](https://learn.microsoft.com/en-us/entra/identity/enterprise-apps/saml-vs-oidc-decision-guide)
