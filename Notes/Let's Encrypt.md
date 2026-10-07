---
title: "Let's Encrypt"
date: 2026-10-07
tags:
  - Security
  - Encryption
related:
  - "[[HTTP・HTTPS]]"
  - "[[TLSサーバ証明書]]"
  - "[[DNS]]"
  - "[[ネットワーク]]"
  - "[[Docker]]"
  - "[[Kubernetes]]"
---

## 概要

Let's Encrypt は，非営利組織 ISRG（Internet Security Research Group）が運営する，無料・自動化・オープンな認証局（CA）である．ACME プロトコル（RFC 8555）で証明書の発行と更新を自動化し，HTTPS の普及を大きく押し上げた．

## 詳細

### 設立経緯と規模

- ISRG は 2013 年設立，Let's Encrypt プロジェクトは 2014 年 11 月に発表され，2015 年 9 月 14 日に最初の公的に信頼される証明書を発行した．初期スポンサーは Mozilla，EFF，Cisco，Akamai，IdenTrust など．
- 2016 年 3 月に累計 100 万枚，2018 年 9 月に 1 日 100 万枚，2020 年に累計 10 億枚．2025 年 9 月には 1 日あたり 1000 万枚を超えた．
- 公式の 10 周年記事によると，世界の HTTPS 利用率は 5 年ほどで 30% 未満から約 80% に上昇し，その水準で安定している．発行枚数では世界最大の CA で，保護するサイト数は 10 億に近づいているとされる．

### ACME プロトコルとチャレンジ方式

ACME は RFC 8555（2019 年 3 月）で標準化され，ドメイン検証と証明書発行を自動化する．ACMEv1 は 2021 年 6 月に終了し，現在はクライアントが ACMEv2 に対応している必要がある．

| 方式 | ポート／場所 | ワイルドカード | 特徴 |
|---|---|---|---|
| HTTP-01 | 80 番のみ | 不可 | `/.well-known/acme-challenge/<TOKEN>` にファイルを置く．最も一般的で自動化しやすい |
| DNS-01 | DNS の TXT レコード | 可 | DNS API の認証情報の管理が必要．ワイルドカードはこの方式のみ |
| TLS-ALPN-01 | 443 番 | 不可 | 独自の ALPN で応答する．TLS 終端するリバースプロキシや大規模ホスティング向け |

RFC 8555 自体が定義するのは http-01 と dns-01 である（TLS-ALPN-01 は別 RFC）．毎回の更新で TXT を変えずに済む新方式 DNS-PERSIST-01 も準備されている．`_validation-persist.<ドメイン>` に CA 名と ACME アカウント URI を書いた TXT を一度置けばよい．CA/Browser Forum の SC-088v3 が 2025-10 に可決され，公式ブログ（2026-02-18）は本番展開を 2026 Q2 目標としていた．しかし 2026-06-25 のコミュニティでのスタッフ発言では，仕様上の未解決課題が片付くまで本番展開しないとされている（[[DNS]] 参照）．

### クライアント

公式は「多くの人はまず Certbot から」と推奨している．そのほか acme.sh，Lego，Caddy（組み込み ACME），Traefik，cert-manager（[[Kubernetes]]），win-acme，Posh-ACME などがある．

### 証明書の有効期間と短縮計画

- 現在の既定は 90 日．更新は 60 日ごとが推奨されている（FAQ）．
- 公式ブログ（2025-12-02）の予定:
  - 2026-05-13: オプトインの tlsserver プロファイルを 45 日証明書に切り替え（profiles ドキュメントでは現在 45 日，認可再利用 7 時間となっており実施済み）
  - 2027-02-10: 既定の classic プロファイルを 64 日証明書（認可の再利用期間 10 日）に切り替え
  - 2028-02-16: classic プロファイルを 45 日証明書（認可の再利用期間 7 時間）に切り替え
- 既定の classic プロファイルは現在 90 日，認可再利用期間 30 日．
- 短命証明書（shortlived プロファイル）は有効期間 160 時間（約 6 日）で，IPv4/IPv6 の IP アドレス証明書とともに 2026 年 1 月に一般提供された．IP アドレス証明書は短命証明書でなければならない．オプトインで，既定にする予定は現時点でないとされる．

### 失効確認の方針変更（OCSP 終了）

- 2024 年 12 月に告知し，2025 年 8 月 6 日に OCSP サービスを終了した．
- 理由はプライバシー（CA が閲覧先サイトと訪問者 IP を把握できる）．今後の失効情報は CRL のみで提供する．
- ピーク時は月約 3400 億リクエスト（CDN 上で毎秒 14 万超）を処理していた．Akamai が 10 年間 CDN を寄贈した．

### レート制限（公式 docs より）

- 新規オーダー: アカウントあたり 3 時間で 300 件（36 秒に 1 件回復）
- 登録ドメインあたりの証明書: 7 日で 50 枚（202 分に 1 枚回復）
- 同一ドメイン集合の重複証明書: 7 日で 5 枚（34 時間に 1 枚回復）
- 認可失敗: 識別子・アカウントあたり 1 時間で 5 回（12 分に 1 回回復）
- 連続認可失敗: 1,152 回まで（1 日に 1 回回復）
- アカウント作成: 同一 IP で 3 時間に 10 件，/48 の IPv6 で 3 時間に 500 件

### 運用のベストプラクティス

- 自動更新（cron / systemd timer / Caddy や cert-manager の組み込み機構）を使い，手動更新を前提にしない．有効期間が短くなる方向なので自動化は必須になる．
- 開発・テストではステージング環境を使い，本番のレート制限を消費しない（既存知識）．
- ワイルドカードや 80 番を開けられない環境では DNS-01．DNS の認証情報は権限を絞った API トークンにする．
- 証明書の期限切れ通知メールは 2025-06-04 に終了し，ACME API で登録されたメールアドレスも削除された．期限の監視は自前か外部サービスで用意する．
- 検証元 IP は非公開かつ変わりうる．複数の場所から検証されるため，IP 許可リストに頼らない．
- コンテナ環境では [[Docker]] のボリュームや [[Kubernetes]] の cert-manager で証明書を永続化・自動更新する（既存知識）．

## ポイント

- 無料・自動・オープンな CA で，ISRG（非営利）が運営する．
- ワイルドカードは DNS-01 のみ．
- 既定の有効期間は 90 日から 2028 年に 45 日へ段階的に短縮．
- OCSP は 2025-08-06 に終了し，CRL に一本化された．
- 6 日証明書と IP アドレス証明書が利用可能．

## 関連項目

- [[TLSサーバ証明書]]
- [[HTTP・HTTPS]]
- [[DNS]]
- [[ネットワーク]]
- [[Docker]]
- [[Kubernetes]]

## 参考

- [Let's Encrypt: From 90 to 45](https://letsencrypt.org/2025/12/02/from-90-to-45)
- [OCSP Service Has Reached End of Life](https://letsencrypt.org/2025/08/06/ocsp-service-has-reached-end-of-life)
- [10 Years of Let's Encrypt Certificates](https://letsencrypt.org/2025/12/09/10-years)
- [6-day and IP address certificates GA](https://letsencrypt.org/2026/01/15/6day-and-ip-general-availability)
- [Rate Limits](https://letsencrypt.org/docs/rate-limits/)
- [Challenge Types](https://letsencrypt.org/docs/challenge-types/)
- [FAQ](https://letsencrypt.org/docs/faq/)
- [ACME Client Implementations](https://letsencrypt.org/docs/client-options/)
- [About Let's Encrypt](https://letsencrypt.org/about/)
- [Profiles](https://letsencrypt.org/docs/profiles/)
- [DNS-PERSIST-01 (Let's Encrypt)](https://letsencrypt.org/2026/02/18/dns-persist-01)
- [dns-persist-01 deployment status and timeline (Community)](https://community.letsencrypt.org/t/dns-persist-01-deployment-status-and-timeline/246468)
- [Expiration Notification Service Has Ended](https://letsencrypt.org/2025/06/26/expiration-notification-service-has-ended)
- [RFC 8555](https://www.rfc-editor.org/rfc/rfc8555)
