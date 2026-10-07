---
title: "DNS"
date: 2026-10-07
tags:
  - Web
  - Security
related:
  - "[[ネットワーク]]"
  - "[[HTTP・HTTPS]]"
  - "[[キャッシュ]]"
  - "[[ゼロトラストセキュリティ]]"
  - "[[Kubernetes]]"
  - "[[TLSサーバ証明書]]"
  - "[[Let's Encrypt]]"
  - "[[Samba]]"
---

## 概要
DNS（Domain Name System）は，ドメイン名と IP アドレス等の情報を対応付ける，階層的で分散したデータベースとその問い合わせプロトコルである．RFC 1034 / 1035（1987 年）で現在の基本形が定義され，Web・メール・証明書発行などインターネットのほぼすべての通信の入口になっている．

## 詳細

### 歴史と背景
- 初期の ARPANET では，ホスト名とアドレスの対応を NIC が単一の `HOSTS.TXT` で管理していたが，ネットワークの拡大で維持できなくなった
- これを解決するため，P. Mockapetris が RFC 1034（概念）と RFC 1035（実装・仕様）を 1987 年 11 月に発行した
- RFC 1034 は DNS の 3 要素を「ドメイン名空間とリソースレコード」「ネームサーバ」「リゾルバ」と定義している
- 設計上，意図的に拡張可能であり，その後 DNSSEC，EDNS(0)，SVCB / HTTPS レコードなどが追加されてきた

### 階層構造
- 名前空間は木構造で，ルート（`.`）→ TLD（`com`，`jp` など）→ セカンドレベル（`example.com`）→ ... と続く
- 各ゾーンは権威サーバ（authoritative server）が管理し，上位ゾーンが NS レコードで下位ゾーンへ権限を委任（delegation）する
- ルートサーバは論理的に 13 系統（a～m.root-servers.net）で，anycast により多数の拠点で運用される（既存知識）

### リゾルバの種類と名前解決の流れ
- スタブリゾルバ: OS やアプリ内の簡易クライアント．再帰的な解決は行わず，フルリゾルバへ問い合わせるだけ
- フルリゾルバ（再帰リゾルバ，キャッシュ DNS サーバ）: ISP やパブリック DNS（1.1.1.1，8.8.8.8 など）が提供．ルートから順に辿って答えを得てキャッシュする
- 権威サーバ: 自ゾーンの情報について答える．再帰はしない

`www.example.com` の A レコードを引く場合（キャッシュが空のとき）:
1. スタブ → フルリゾルバへ再帰クエリ
2. フルリゾルバ → ルートサーバ: `com` の NS を返す（referral）
3. フルリゾルバ → `com` TLD サーバ: `example.com` の NS を返す
4. フルリゾルバ → `example.com` の権威サーバ: A レコードを返す
5. フルリゾルバは結果を TTL の間キャッシュし，スタブへ返す

### 主要レコード
| タイプ | 用途 |
|---|---|
| A / AAAA | ホスト名 → IPv4 / IPv6 アドレス |
| CNAME | 別名．正規名へのエイリアス．同名に他のレコードは置けない（ゾーン頂点には置けない）|
| MX | メール配送先と優先度 |
| TXT | 任意テキスト．SPF，DKIM，ドメイン所有確認，ACME DNS-01 などに利用 |
| NS | そのゾーンの権威サーバ / 委任先 |
| SOA | ゾーンの管理情報（シリアル，リフレッシュ間隔，ネガティブキャッシュ TTL 等） |
| CAA | 証明書を発行してよい CA を制限（RFC 8659）|
| SVCB / HTTPS | サービスの接続パラメータを事前に通知（RFC 9460）|

- CAA: 準拠 CA は発行前に関連する CAA RRset の有無を確認する必要があり，適用される CAA がある場合，要求がそれに合致しない限り発行してはならない（MUST NOT）
- SVCB（型 64）と HTTPS（型 65）: AliasMode（優先度 0）と ServiceMode を持つ．HTTPS レコードは HTTP 向けで，`alpn` パラメータで対応プロトコル（HTTP/3 など）を接続前に通知できる．ECH（Encrypted ClientHello）用の `ech` キーは RFC 9460 では予約のみで，仕様は別文書で定められる

### TTL とキャッシュ
- 各レコードは TTL（秒）を持ち，リゾルバはその間結果をキャッシュする．権威サーバ側の負荷と応答遅延を減らす基本機構（[[キャッシュ]] 参照）
- 存在しないこと（NXDOMAIN 等）もキャッシュされる（ネガティブキャッシュ，RFC 2308）．その TTL は SOA の minimum フィールドに基づく
- 変更反映が遅れる最大の原因は TTL．移行前に TTL を短くしておくのが定石
- 注意: 一部のリゾルバは TTL を無視 / 延長することがあり，「伝播」は実際には各キャッシュの期限切れを待つ挙動である（既存知識）

### DNSSEC
- DNS 応答にデジタル署名を付け，データ起源認証とデータ完全性を提供する（RFC 4033）．機密性は提供しない．DoS 対策にもならない
- 主要レコード: DNSKEY，RRSIG，DS（親ゾーンに置いて信頼の連鎖を作る），NSEC / NSEC3（不在証明）
- 信頼の連鎖はルートのトラストアンカー（KSK）から TLD，ドメインへと続く．検証はフルリゾルバが行う
- 普及率は TLD によって差が大きく，運用の複雑さ（鍵ロールオーバー失敗による名前解決不能）が課題（既存知識）

### 暗号化 DNS
従来の DNS（UDP / TCP 53）は平文で，経路上で盗聴・改ざんされ得る．

| 方式 | RFC | 概要 |
|---|---|---|
| DoT | RFC 7858 | TLS 上で DNS．既定ポート TCP 853 |
| DoH | RFC 8484 | HTTPS 上で DNS．メディアタイプ `application/dns-message`，ポート 443．通常の HTTPS に紛れ，ブロックしにくい |
| DoQ | RFC 9250 | QUIC 上で DNS．既定ポート UDP 853．TCP の head-of-line blocking を回避 |

- 暗号化されるのはクライアント〜リゾルバ間のみ．リゾルバ〜権威サーバ間は別問題である（既存知識）
- DoH は企業ネットワークでの DNS フィルタリングやログ取得を迂回し得るため，[[ゼロトラストセキュリティ]] や内部ポリシーとの整合が論点になる

### 代表的な攻撃
- キャッシュポイズニング: 偽の応答をリゾルバのキャッシュに注入する．2008 年の Kaminsky 攻撃は，存在しないサブドメインを大量に問い合わせて 16 ビットのクエリ ID を総当たりする手法（既存知識）．対策として RFC 5452（2009 年）は，クエリ ID を全範囲（0〜65535）で予測不能にし，送信元ポートも広い範囲でランダム化することを求めている．これで攻撃者が推測すべき空間が ID の 16 ビットより大きくなる
- その後も IP フラグメンテーションを利用した攻撃やサイドチャネル攻撃（2020 年の SAD DNS）が研究されている（既存知識）．DNSSEC 検証が根本的な対策
- その他（既存知識）: DNS アンプ攻撃（DDoS の反射増幅），DNS トンネリング，ドメインハイジャック，サブドメインテイクオーバー，DNS リバインディング

### 実践

#### dig の使い方
```sh
dig example.com            # A レコード
dig example.com AAAA +short
dig example.com MX
dig @1.1.1.1 example.com   # リゾルバ指定
dig +trace example.com     # ルートから委任を辿る
dig example.com DNSKEY +dnssec   # DNSSEC 情報
dig -x 192.0.2.1           # 逆引き
dig example.com CAA
dig _acme-challenge.example.com TXT
```
- 出力の ANSWER SECTION の数値は残り TTL．`status:`（NOERROR / NXDOMAIN / SERVFAIL）と `flags`（`aa` 権威，`ad` DNSSEC 検証済み）を見る
- 現代では `dog`，`doggo` などの代替ツールもある（既存知識）

#### ローカル DNS
- `/etc/hosts` が DNS より先に参照される（`nsswitch.conf` / OS 設定次第）
- ローカルキャッシュ・自前リゾルバ: dnsmasq，Unbound，systemd-resolved，CoreDNS など
- [[Kubernetes]]: クラスタ内 DNS として CoreDNS が動き，`<service>.<namespace>.svc.cluster.local` でサービス名前解決を行う
- スプリットホライズン DNS（内外で異なる応答を返す）は社内システムでよく使われる

#### 証明書発行との関係（DNS-01 / CAA）
- ACME の DNS-01 では，ドメイン制御の証明として `_acme-challenge.<ドメイン>` に TXT レコードを置く．HTTP-01 と異なりワイルドカード証明書を発行でき，公開 Web サーバがなくても検証できる．一方，DNS プロバイダの API 認証情報の管理がリスクとなり，API を持たない DNS プロバイダでは使いにくい（[[Let's Encrypt]]，[[TLSサーバ証明書]]）
- CAA レコードにより，ドメイン所有者は発行を許可する CA を制限できる．例: `example.com. CAA 0 issue "letsencrypt.org"`（既存知識に基づく記法例）
- [[HTTP・HTTPS]] の通信は DNS が起点であり，DNS 改ざんは証明書の不正発行（DV の悪用）にもつながり得るため，DNSSEC・CAA・Certificate Transparency を組み合わせて防御する（既存知識）

### 最近の動向
- 2026 年 10 月 11 日: ルートゾーン KSK ロールオーバー．KSK-2024（キータグ 38696）が唯一のルート DNSKEY の署名鍵となる予定（旧 KSK-2017 はキータグ 20326）．ICANN は，DNSSEC 検証リゾルバの運用者に KSK-2024 がトラストアンカーに含まれているか確認するよう求めており，自動更新が成功したと仮定しないよう注意している．含まれていなければ検証に失敗し，名前解決できなくなるおそれがある
- DNS-PERSIST-01: Let's Encrypt が 2026-02-18 に発表した新しい ACME チャレンジ．`_validation-persist.<ドメイン>` に CA と ACME アカウント URI を示す TXT を一度置けば，以後の発行・更新で DNS 更新が不要になる．CA/B Forum の SC-088v3 が 2025 年 10 月に可決された．発表時点では本番展開を 2026 年 Q2 目標としていたが，2026-06-25 のコミュニティでのスタッフ発言では，仕様上の未解決課題が片付くまで本番展開しないとされている
- SVCB / HTTPS レコード（RFC 9460, 2023 年）の普及と，それを基盤とする ECH，HTTP/3 の発見
- 暗号化 DNS の標準化進展（DoQ は RFC 9250）

## ポイント
- DNS は階層的な委任と各層でのキャッシュにより，スケールと低遅延を実現している
- スタブ / フルリゾルバ / 権威サーバの役割分担を押さえると，障害切り分けがしやすい
- 変更が反映されない原因の多くは TTL とキャッシュ
- DNSSEC は真正性・完全性，DoT / DoH / DoQ は機密性．両者は補完関係
- CAA と DNS-01 により DNS は TLS 証明書発行の信頼の根拠にもなっている．DNS を守ることが証明書の安全性に直結する
- DNS-01 は DNS API 認証情報の管理に注意．DNS-PERSIST-01 は新たな選択肢
- `dig +trace` と `dig @resolver` が基本の切り分け手段

## 関連項目
- [[ネットワーク]]
- [[HTTP・HTTPS]]
- [[キャッシュ]]
- [[ゼロトラストセキュリティ]]
- [[Kubernetes]]
- [[TLSサーバ証明書]]
- [[Let's Encrypt]]
- [[DNSSEC]]
- [[CoreDNS]]
- [[Samba]]

## 参考
- [RFC 1034: Domain Names - Concepts and Facilities](https://www.rfc-editor.org/rfc/rfc1034)
- [RFC 5452: Measures for Making DNS More Resilient against Forged Answers](https://www.rfc-editor.org/rfc/rfc5452)
- [RFC 2308: Negative Caching of DNS Queries](https://www.rfc-editor.org/rfc/rfc2308)
- [RFC 4033: DNS Security Introduction and Requirements](https://www.rfc-editor.org/rfc/rfc4033)
- [RFC 7858: DNS over TLS](https://www.rfc-editor.org/rfc/rfc7858)
- [RFC 8484: DNS over HTTPS](https://www.rfc-editor.org/rfc/rfc8484)
- [RFC 8659: DNS CAA Resource Record](https://www.rfc-editor.org/rfc/rfc8659)
- [RFC 9250: DNS over QUIC](https://www.rfc-editor.org/rfc/rfc9250)
- [RFC 9460: SVCB and HTTPS Resource Records](https://www.rfc-editor.org/rfc/rfc9460)
- [Let's Encrypt: Challenge Types](https://letsencrypt.org/docs/challenge-types/)
- [Let's Encrypt: DNS-PERSIST-01](https://letsencrypt.org/2026/02/18/dns-persist-01)
- [ICANN: Root Zone KSK Rollover](https://www.icann.org/resources/pages/ksk-rollover-en)
- [dns-persist-01 deployment status and timeline (Let's Encrypt Community)](https://community.letsencrypt.org/t/dns-persist-01-deployment-status-and-timeline/246468)
