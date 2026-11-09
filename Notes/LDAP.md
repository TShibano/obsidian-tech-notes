---
title: "LDAP"
date: 2026-11-09
tags:
  - Security
  - Auth
  - DB
  - NoSQL
related:
  - "[[Active Directory]]"
  - "[[SSO]]"
  - "[[SAML]]"
  - "[[OpenID Connect]]"
  - "[[TLSサーバ証明書]]"
  - "[[電子証明書]]"
  - "[[ゼロトラストセキュリティ]]"
  - "[[ネットワーク]]"
  - "[[DNS]]"
  - "[[Samba]]"
  - "[[SAP]]"
---

## 概要

LDAP（Lightweight Directory Access Protocol）は，ディレクトリサービスにアクセスするためのアプリケーション層プロトコル．ユーザー，グループ，機器などの情報を木構造（DIT）で保持するディレクトリに対し，認証（bind）や検索（search）を行う．現行の LDAPv3 は RFC 4510 系（2006年6月）で規定され，社内の認証・認可基盤の中核として [[Active Directory]] や OpenLDAP などに実装されている．

## 詳細

### 仕様（RFC 4510 系）

RFC 4510 は LDAPv3 技術仕様のロードマップで，以下の RFC 群（いずれも 2006年6月）を束ねる．

| RFC | 内容 |
|-----|------|
| 4511 | プロトコル本体（操作とエンコーディング） |
| 4512 | ディレクトリ情報モデル（エントリ，属性，スキーマ，DIT） |
| 4513 | 認証方式とセキュリティ機構（simple bind，SASL，TLS） |
| 4514 | DN の文字列表現 |
| 4515 | 検索フィルタの文字列表現 |
| 4516 | LDAP URL |
| 4517 | 構文とマッチングルール |
| 4518 | 国際化文字列の準備 |
| 4519 | ユーザーアプリケーション向けスキーマ（inetOrgPerson 等） |

### データモデル

- **エントリ**: ディレクトリの 1 件のデータ．属性（attribute）と値の集合を持つ
- **DIT（Directory Information Tree）**: エントリを階層化した木．ルートに近い側が組織，下位が部署やユーザー
- **DN（Distinguished Name）**: エントリを一意に識別する名前．例 `uid=taro,ou=People,dc=example,dc=com`．各階層の RDN をカンマで連結する
- **objectClass とスキーマ**: エントリが持てる／持つべき属性を objectClass が定め，スキーマ（属性型，構文，マッチングルール）が型を定義する
- **LDIF**: エントリをテキストで表すデータ交換形式（RFC 2849）

### 主な操作

| 操作 | 説明 |
|------|------|
| Bind | 認証．anonymous / simple（DN + パスワード）/ SASL（Kerberos(GSSAPI)，EXTERNAL 等） |
| Search | base DN，scope（base / one / sub），フィルタ，取得属性を指定して検索 |
| Compare | 属性値の一致確認 |
| Add / Modify / Delete / Modify DN | エントリの追加・変更・削除・名前変更 |
| Extended | StartTLS，パスワード変更など拡張操作 |
| Unbind / Abandon | 接続終了／操作中止 |

### 通信の保護（LDAPS と StartTLS）

- **LDAPS**: 接続開始時点から TLS で暗号化（既定 636/tcp）
- **StartTLS**: 389/tcp で平文接続した後，拡張操作で TLS に昇格する．クライアント設定ミスで平文のまま動いてしまう危険があるとして，LDAPS のみに限定すべきという主張もある
- simple bind を平文で行うと資格情報が盗聴されるため，TLS の確立と証明書検証を bind より前に行う．サーバー証明書は [[TLSサーバ証明書]] を参照
- RFC 9266（TLS 1.3 向けチャネルバインディング，tls-exporter）は，TLS 1.3 でも SASL 認証を TLS セッションに結び付けるための仕様

### 主な実装

| 実装 | 特徴 |
|------|------|
| OpenLDAP | 代表的な OSS．2.6 と 2.7 が現行リリース（2026-11 時点）．2.7 は 2026-08 に GA |
| 389 Directory Server | Red Hat 系の OSS．Red Hat Directory Server の上流．複数ブランチが並行（3.2.x，3.3.x など） |
| [[Active Directory]] | Microsoft のディレクトリ．LDAP インターフェースに加え Kerberos，独自拡張を持つ |
| [[Samba]] AD DC | AD 互換のドメインコントローラーを OSS で提供 |

### 認証基盤での使われ方

- アプリ側から見た定番は **search-then-bind**: サービスアカウントで bind しユーザー DN を検索 → 取得した DN とユーザーのパスワードで再 bind して認証
- グループ所属（memberOf 等）を取得して認可に使う
- VPN，Wi-Fi（802.1X / RADIUS），Linux の SSSD，Web アプリ，Git ホスティングなど多くの製品が LDAP 連携を持つ
- 近年のクラウド／SaaS 連携は [[SAML]] や [[OpenID Connect]] で [[SSO]] を行い，LDAP は IdP の背後のユーザーストアとして使われる構成が多い

### 最新動向

#### Windows の LDAP 署名・チャネルバインディング

Microsoft Learn（2026-10-02 更新）によれば，LDAP の保護層は次の4つに整理される．

| 保護 | 目的 | 適用範囲 |
|------|------|----------|
| LDAP signing | メッセージの完全性・真正性 | TLS を使わない SASL セッション（389） |
| SASL sealing | 機密性 + 完全性 | TLS を使わない SASL セッション |
| TLS（LDAPS / StartTLS） | 機密性，完全性，サーバー識別 | すべてのセッション種別 |
| チャネルバインディング（CBT） | 認証を TLS セッションに結び付け，リレー攻撃を防ぐ | TLS + SASL のみ（主に NTLM リレー対策） |

- LDAP signing は simple bind を保護しない．そのため TLS を使うか，Kerberos の SASL を推奨している
- **Windows Server 2025 以降**: 新規 AD 展開では LDAP 署名が既定で必須．チャネルバインディングは既定で「When supported」，監査は既定で有効．Windows Server 2025 / Windows 11 24H2 以降の LDAP クライアントは既定で暗号化接続を使う
- **Windows Server 2022 以前**: LDAP 署名は任意（署名なしも受け入れ），チャネルバインディングは「Never」．ただし 2023年3月の更新で CBT の監査が既定で有効化された（ADV190023 に基づく段階的な強制）
- **アップグレード時**: 既存のポリシーは維持される．ポリシー未設定のサーバーを 2025 へ上げた場合は署名が既定で必須になる
- チャネルバインディングのポリシー値（`LdapEnforceChannelBinding`）: 0 = Never，1 = When supported，2 = Always
- 移行手順は，イベント 2887（許可された署名なし bind の 24時間集計）や 2889（クライアント別の詳細，診断ログ 16 LDAP Interface Events をレベル 2 にして取得）で署名なしクライアントを洗い出し，修正後に必須化する．チャネルバインディングはイベント 3039，3074，3075 等で確認する
- NTLM と Digest は非推奨で，Kerberos を使うよう案内されている

#### OpenLDAP 2.7

2026-08-06 に GA．公式発表の主な内容:

- libldap: ldaps の DNS SRV 対応，TPM / スマートカード / HSM / mbedTLS 対応
- back-mdb: LMDB 1.0 に移行し，ページ単位の DB 暗号化とチェックサムに対応
- ACL に increment 権限を追加．ppolicy オーバーレイに動的ポリシー選択などを追加
- 廃止予定: GnuTLS（2.8 で削除予定）．削除済み: back-perl，back-sql
- ロードマップ上は 2.8 が 2027 年に計画中

#### 389 Directory Server 3.3.1

複数の CVE 修正に加え，Have I Been Pwned によるパスワード漏えい検証，レプリケーション関連の改善などが含まれる（公式リリースノート）．

## ポイント

- LDAP は「ディレクトリ（木構造のデータストア）にアクセスするプロトコル」であり，認証専用ではない．認証は bind 操作の一用途
- simple bind は必ず TLS（LDAPS または StartTLS）の上で使う
- AD では，TLS を使う場合は署名ではなくチャネルバインディングが NTLM リレー対策になる．TLS を使わない SASL では署名が完全性を担う
- Windows Server 2025 の新規環境は署名必須が既定．古い LDAP クライアントや機器（複合機，NAS，ネットワーク機器など）が simple bind を平文で使っていると接続できなくなる．移行前にイベント監査で洗い出す
- 新規システムでは，クラウド連携に [[SAML]] / [[OpenID Connect]] を使い，LDAP は社内ユーザーストア連携に限定する構成が一般的

## 関連項目

- [[Active Directory]]
- [[SSO]]
- [[SAML]]
- [[OpenID Connect]]
- [[TLSサーバ証明書]]
- [[電子証明書]]
- [[ゼロトラストセキュリティ]]
- [[Samba]]
- [[DNS]]
- [[ネットワーク]]
- [[SAP]]

## 参考

- [RFC 4510: LDAP: Technical Specification Road Map](https://www.rfc-editor.org/rfc/rfc4510.html)
- [RFC 9266: Channel Bindings for TLS 1.3](https://www.rfc-editor.org/rfc/rfc9266)
- [LDAP Signing Overview for Active Directory (Microsoft Learn)](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/ldap-signing)
- [LDAP Channel Binding in Active Directory (Microsoft Learn)](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/ldap-channel-binding)
- [OpenLDAP Release Road Map](https://mail.openldap.org/software/roadmap.html)
- [OpenLDAP Version 2.7 Feature Release Announcement](https://lists.openldap.org/hyperkitty/list/openldap-announce@openldap.org/thread/MSTQ4QG6TJ7K25GCBOXGCOEHWOHLB3ZH/)
- [389 Directory Server 3.3.1](https://www.port389.org/docs/389ds/releases/release-3-3-1.html)
