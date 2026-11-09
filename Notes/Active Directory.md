---
title: "Active Directory"
date: 2026-11-09
tags:
  - Security
  - Auth
  - Cloud
  - Azure
related:
  - "[[LDAP]]"
  - "[[SAML]]"
  - "[[OpenID Connect]]"
  - "[[Samba]]"
  - "[[DNS]]"
  - "[[ゼロトラストセキュリティ]]"
  - "[[電子証明書]]"
  - "[[Kerberos]]"
  - "[[Microsoft Entra ID]]"
  - "[[SAP]]"
---

## 概要

Active Directory Domain Services（AD DS）は，Windows Server に搭載されている Microsoft のディレクトリサービスで，ユーザー・コンピューター・グループなどのオブジェクトを一元管理し，認証（Kerberos / NTLM）とポリシー適用（グループポリシー）を提供する．Windows 2000 で導入され，現在も企業のオンプレミス ID 基盤の中核だが，クラウド側の ID 基盤は Microsoft Entra ID（旧 Azure AD）が担い，両者を同期するハイブリッド構成が一般的になっている．

## 詳細

### 論理構造

| 要素 | 説明 |
|------|------|
| フォレスト | 最上位のコンテナー．セキュリティ境界であり，管理権限の範囲を定義する．既定では単一のドメイン（フォレストルートドメイン）を含む |
| ドメイン | 管理とリソースのグルーピングの単位 |
| ツリー | 連続した DNS 名前空間を共有するドメインの階層 |
| OU（組織単位） | ドメイン内の管理単位．管理権限の委任とグループポリシーの適用単位として使う |
| ドメインコントローラー（DC） | AD DS を実行するサーバー．ドメインのディレクトリ情報の完全なコピーを持ち，マルチマスターでレプリケーションする |
| グローバルカタログ | フォレスト内の他ドメインのオブジェクト検索を高速化する |
| FSMO | スキーママスターとドメイン命名マスターはフォレストに 1 台，PDC エミュレーターはドメインごとに 1 台 |

- ディレクトリは ESE データベース（NTDS.dit）に格納され，DNS を使って DC を探す（SRV レコード）．そのため AD は [[DNS]] に強く依存する
- スキーマ拡張，サイト（IP サブネットに基づくレプリケーションと DC 選択の単位）といった概念もある

### 認証プロトコルとの関係

- **Kerberos**: ドメインの既定の認証方式．DC が KDC として動作しチケットを発行する．[[Kerberos]] 参照
- **NTLM**: 旧来のチャレンジレスポンス認証．Kerberos が使えない場合（DC に到達できない，SPN が不明，IP アドレス指定，ローカルアカウントなど）のフォールバックとして使われてきた
- **LDAP**: ディレクトリへの問い合わせ・更新プロトコル．[[LDAP]] 参照．Windows Server 2025 では新規 AD 展開で SASL バインド後の LDAP 署名（シーリング）が既定で必須になった
- **グループポリシー（GPO）**: ドメイン，サイト，OU に対して設定を配布する．SYSVOL を介して配布される

### Microsoft Entra ID との違いとハイブリッド構成

Entra ID は「クラウド上の AD」ではなく別の ID プラットフォームで，OAuth 2.0 / [[SAML]] / [[OpenID Connect]] / WS-* でクラウドアプリを統合する．AD DS は LDAP / Kerberos / NTLM でオンプレミスのアプリ・インフラを支える．

| 観点 | AD DS | Microsoft Entra ID |
|------|-------|--------------------|
| 主な用途 | オンプレミスのインフラ，レガシーアプリ | クラウド / SaaS / モダン認証アプリ |
| アプリ連携 | LDAP，Kerberos，NTLM．SaaS には AD FS などのフェデレーションが必要 | OAuth2，SAML，WS-*．アプリプロキシでオンプレミスアプリも公開可能 |
| デバイス | ドメイン参加 + グループポリシー | Entra 参加 + Intune，条件付きアクセス |
| 管理委任 | ドメイン・OU・グループ | 組み込みロールの RBAC，PIM |
| サービスアカウント | サービスアカウント，gMSA | マネージド ID |

ハイブリッド構成の要素:

- **Microsoft Entra Connect / Cloud Sync**: オンプレミス AD の ID を Entra ID に同期する．Cloud Sync は軽量エージェントで構成をクラウド側に持ち，新規導入向けとされる（Connect Sync は複雑な属性フィルタリング向け）．
- **Microsoft Entra Kerberos**: Entra ID をクラウドの KDC として使い，ハイブリッドユーザーが DC への直接の到達性なしで Kerberos によりリソースへアクセスできる
- **Microsoft Entra Domain Services**: LDAP / GPO / Kerberos に依存するレガシーアプリ向けのマネージドドメインサービス
- 認証をクラウドに寄せる場合でも，オンプレミスリソースが残る限り AD DS は残る．パスワードや条件付きアクセス等のクラウド側統制と組み合わせて使う

### 最新動向（2026-11-09 時点）

**Windows Server 2025 の AD DS 機能**（Microsoft Learn）

- 新しいフォレスト / ドメイン機能レベル（`DomainLevel 10` / `ForestLevel 10`）．32k データベースページ機能に必要
- **32k データベースページ（オプション機能）**: Windows 2000 以来の ESE 8k ページ設計による制限を緩和する．新規 DC は 32k ページ・64bit LID で構築でき，8k ページモードで互換動作する．アップグレードされた DC は従来の 8k のまま．移行はフォレスト全体で行い，全 DC が 32k 対応である必要がある
- **NUMA 対応**: 全プロセッサーグループの CPU を使用でき，64 コアを超えて拡張できる
- **dMSA（委任マネージドサービスアカウント）**: 従来のサービスアカウントからの移行を可能にし，ランダム化されたキーを管理する．元のサービスアカウントのパスワードは無効化される
- **セキュリティ強化**: LDAP over TLS 1.3 対応，機密属性の LDAP 操作は暗号化接続のみ許可，LDAP チャネルバインディング監査（イベント 3074 / 3075），ランダムな既定のマシンアカウントパスワードと既定パスワードの設定拒否，Kerberos KDC が RC4 での TGT を発行しない，PKINIT の暗号アジリティ，SMB 署名の既定必須化，リモートメールスロットの既定無効化
- 新規フォレストの機能レベルは Windows Server 2016 以上が必須
- Windows LAPS の強化（イメージロールバック検出，パスフレーズなど）

**NTLM 廃止方針**（Windows IT Pro Blog）

NTLM は完全削除ではなく「既定で無効化」する方針．3 フェーズで進む．

| フェーズ | 時期 | 内容 |
|----------|------|------|
| 1 | 提供中 | 拡張 NTLM 監査（Windows Server 2025 / Windows 11 24H2 以降）で NTLM の使用箇所を可視化 |
| 2 | 2026 年後半 | IAKerb（DC に到達できなくても Kerberos を成功させる）と Local KDC（ローカルアカウントのサインインで NTLM にフォールバックさせない）．コアコンポーネントを Kerberos 優先に更新 |
| 3 | 次期メジャー Windows Server と対応クライアント | ネットワーク NTLM を既定でブロック．OS には残り，ポリシーで再有効化可能 |

なお Windows Server 2025 では SMB クライアントがリモート送信接続の NTLM ブロックに対応した．

**Samba AD**

Samba 4.0 以降は AD ドメインコントローラーとして動作でき，Windows クライアントを参加させられる．4.24 系（2026-03-18 に 4.24.0 リリース，同系列の最新は 2026-10-12 の 4.24.8．後継の 4.25 系もリリース済み）では，Entra ID / Keycloak からのパスワードリセットでポリシーヒント制御に従う，Windows Hello for Business の Key-Trust ログオン対応（組み込み Heimdal KDC），PKINIT の強い証明書マッピング（既定 "full"），ドメイン暗号化タイプの既定を AES のみにする（CVE-2026-20833 対応）といった変更が入った．

## ポイント

- AD DS は「フォレスト > ドメイン > OU」の階層と，マルチマスターレプリケーションする DC で構成される
- 認証は Kerberos が基本で，NTLM はフォールバック．NTLM は次期 Windows Server で既定無効になる予定なので，今のうちに拡張監査で依存箇所を洗い出す
- Entra ID は AD の置き換えではなく別物．ハイブリッドでは Entra Connect / Cloud Sync で同期し，オンプレミス資産が残る限り AD DS も運用する
- Windows Server 2025 の新機能（32k ページ，dMSA 等）は機能レベル 10 や全 DC の対応が条件になるものがある
- AD は [[DNS]] に強く依存し，侵害時の影響が大きいため，特権アカウントの保護，LDAP 署名 / チャネルバインディング，RC4 の排除が重要
- Linux 中心の環境では Samba AD でも AD 互換のドメインを構築できる

## 関連項目

- [[LDAP]]
- [[SAML]]
- [[OpenID Connect]]
- [[Samba]]
- [[DNS]]
- [[ゼロトラストセキュリティ]]
- [[電子証明書]]
- [[Kerberos]]
- [[Microsoft Entra ID]]
- [[SAP]]

## 参考

- [What's new in Windows Server 2025 - Microsoft Learn](https://learn.microsoft.com/windows-server/get-started/whats-new-windows-server-2025)
- [Advancing Windows security: Disabling NTLM by default - Windows IT Pro Blog](https://techcommunity.microsoft.com/blog/windows-itpro-blog/advancing-windows-security-disabling-ntlm-by-default/4489526)
- [Compare Active Directory to Microsoft Entra ID - Microsoft Learn](https://learn.microsoft.com/entra/fundamentals/compare)
- [Samba 4.24 Features added/changed - Samba Wiki](https://wiki.samba.org/index.php/Samba_4.24_Features_added/changed)
- [Active Directory Domain Services Overview - Microsoft TechNet（アーカイブ）](https://technet.microsoft.com/en-us/hh831484)
