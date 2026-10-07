---
title: "TLSサーバ証明書"
date: 2026-10-07
tags:
  - Security
  - Encryption
related:
  - "[[HTTP・HTTPS]]"
  - "[[SSH]]"
  - "[[ネットワーク]]"
  - "[[ゼロトラストセキュリティ]]"
  - "[[SSO]]"
  - "[[Let's Encrypt]]"
  - "[[DNS]]"
---

## 概要

TLSサーバ証明書は，X.509 形式でサーバの公開鍵とドメイン名（またはIPアドレス）を結び付け，認証局（CA）の署名で正当性を保証するデータである．ブラウザなどのクライアントは証明書チェーンをトラストストアのルート証明書まで辿って検証し，通信相手の真正性を確認した上で暗号化通信（[[HTTP・HTTPS]]）を行う．近年は有効期間の大幅な短縮が決まっており，ACME による自動化が事実上必須になりつつある．

## 詳細

### X.509 証明書の構造

RFC 5280 は X.509 v3 証明書と v2 CRL のインターネット向けプロファイルを定める．証明書本体（TBSCertificate）は主に次のフィールドを持つ．

- version: 拡張を使う場合は v3 でなければならない
- serialNumber: CA が発行する証明書ごとの正の整数で，発行者内で一意
- signature: CA が署名に使うアルゴリズム
- issuer: 署名・発行した CA の識別名
- validity: notBefore / notAfter による有効期間
- subject: 公開鍵の持ち主
- subjectPublicKeyInfo: 公開鍵とアルゴリズム（RSA，ECDSA など）
- extensions: subjectAltName（SAN），keyUsage，extendedKeyUsage，CRL 配布点，Authority Information Access，SCT リストなど

実務上，ホスト名の照合は SAN のみで行われ，旧来の Common Name は使われない（既存知識）．SAN には DNS 名のほか IP アドレスも入れられる．署名値は TBSCertificate 全体に対する CA の署名である．

### CA・証明書チェーン・トラストストア

サーバは通常，リーフ証明書（サーバ証明書）と中間 CA 証明書を送り，クライアントはルート CA まで署名を辿る．ルート証明書は OS やブラウザのトラストストア（Chrome Root Store，Mozilla，Apple，Microsoft など）に事前に収録されており，ここに入っている CA だけが公に信頼される．ルート秘密鍵はオフラインで保護し，日常の発行は中間 CA が担う（既存知識）．サーバが中間証明書を送り忘れると，一部のクライアントで検証エラーになる．

### DV / OV / EV

| 種別 | 検証内容 | 備考 |
|---|---|---|
| DV (Domain Validation) | ドメインの管理権限のみ | 自動発行向き．Let's Encrypt はこれ |
| OV (Organization Validation) | ドメイン + 組織の実在性 | 証明書に組織名が入る |
| EV (Extended Validation) | より厳格な組織の審査 | 現在のブラウザは UI 上で区別をほとんどしない |

暗号化の強度は種別によらず同じで，違いは「誰に発行したかをどこまで確認するか」である（既存知識）．

### 失効確認（CRL / OCSP と最近の動向）

- CRL: CA が失効済みのシリアル番号の一覧を署名付きで公開する．
- OCSP（RFC 6960）: 個別の証明書の状態を問い合わせる．CA が訪問先サイトをクライアントIPとともに知り得るというプライバシー上の問題がある．OCSP stapling で緩和できるが，Let's Encrypt は OCSP そのものを廃止した．
- CA/Browser Forum は 2023 年のバロット SC-063v4 で，CA の OCSP 提供を任意とし，CRL の公開を必須とした．同バロットは短命証明書も定義しており，最大有効期間は当初10日，2026-03-15 以降の発行分は7日である．
- Let's Encrypt は 2025-08-06 に OCSP サービスを停止し，以後は失効情報を CRL のみで公開すると発表した．主な理由はプライバシーである．ピーク時には月約3400億件の OCSP リクエストを処理していた．
- 短命証明書は失効手段に頼る必要を減らす．Let's Encrypt の6日（160時間）証明書と IP アドレス証明書は 2026-01 に一般提供が始まった．

### Certificate Transparency（CT）

発行した証明書を公開・追記専用・暗号的に検証可能なログに記録する仕組みで，誤発行や不正発行の検出を早め，CA への監視を強める．ログに登録すると SCT（Signed Certificate Timestamp）が返り，サーバは SCT を証明書拡張，TLS 拡張，OCSP stapling のいずれかでクライアントに渡す．Chrome の CT ポリシーでは，埋め込み SCT の場合，有効期間180日以下なら異なるログからの SCT 2個，180日超なら3個が必要で，少なくとも2つは異なるログ運営者からのものでなければならない．CT の標準化は RFC 6962 に始まり，RFC 9162 で v2.0 が定められている（RFC 本文は未確認）．ドメイン所有者は CT ログを監視して，自ドメインの意図しない証明書発行を検知できる（既存知識）．

### 有効期間短縮の動向（CA/Browser Forum）

2025年4月にバロット SC-081v3 が可決され（発行者 25 賛成・0 反対・5 棄権，消費者は Apple・Google・Microsoft・Mozilla の 4 賛成），Baseline Requirements の 6.3.2 で次の上限が定められた（日付は発行日基準．日程表は BR の redlined PDF による）．

| 発行日 | 最大有効期間 | ドメイン検証情報（SAN）の再利用期間 |
|---|---|---|
| 2026-03-15 より前 | 398日 | 398日 |
| 2026-03-15 以降 2027-03-15 より前 | 200日 | 200日 |
| 2027-03-15 以降 2029-03-15 より前 | 100日 | 100日 |
| 2029-03-15 以降 | 47日 | 10日 |

BR 本文では 1日 = 86,400 秒で数える．今日（2026-10-07）時点で上限は 200日で，次の段階は 2027-03-15 の 100日である．

Let's Encrypt はこれより短い期間を先行して採用しており，オプトインの tlsserver プロファイルで 2026-05-13 から45日証明書を提供している．さらに既定の classic プロファイルを 2027-02-10 に64日（認可再利用10日）へ，2028-02-16 に45日（認可再利用7時間）へ切り替える予定を示している．

関連して Chrome Root Program Policy（v1.8）は，公開 TLS の PKI 階層を TLS サーバ認証専用にする方針を取っている．2026-06-15 以降に CCADB へ開示される下位 CA 証明書と，2027-03-15 以降に発行されるサーバ証明書は，extendedKeyUsage に serverAuth のみを含めなければならない．このため公開 CA の証明書はクライアント認証（mTLS）に使えなくなる方向で，Let's Encrypt も clientAuth を含む tlsclient プロファイルを 2026-07-08 に終了した．クライアント認証には私設 CA が必要になる．

### ACME による自動化

RFC 8555 の ACME は，CA と申請者が検証と発行を自動化するプロトコルで，失効などの管理機能も持つ．検証には http-01（Web パスでの確認），dns-01（[[DNS]] の TXT レコード）などのチャレンジを使う．dns-01 はワイルドカード証明書に必要で，IP アドレス証明書には使えない．クライアントは certbot，acme.sh，Caddy などで，発行元の代表が [[Let's Encrypt]] である．期間が短くなるほど，更新の自動化と失敗時の監視が運用の中心になる．

### 自己署名証明書

発行者と所有者が同一の証明書で，トラストストアに存在しないため公開サービスでは警告が出る．開発・検証や閉域環境，クライアントへ個別に配布する用途に向く．内部システムでは私設 CA（社内 PKI）を立ててルートを配布する方が，証明書ごとの例外登録より管理しやすい（既存知識）．

### 運用上の注意点

- 有効期限切れは障害に直結する．更新は自動化し，期限を監視する．
- 中間証明書を含めたフルチェーンを配信する．
- 秘密鍵は厳重に管理し，漏えい時は失効と再発行を行う．
- 証明書の固定（ピンニング）は短命化と相性が悪い．
- CT ログを監視して不正発行を検知する．
- 内部用途・mTLS は公開 CA に頼らず私設 CA を使う．
- 短い期間では DNS や HTTP 到達性の障害が更新失敗に直結する．

## ポイント

- 構造は RFC 5280．ホスト名は SAN で照合する．
- 信頼はチェーンとルートのトラストストアで成立する．
- 最大有効期間は 2026-03-15 に200日，2027-03-15 に100日，2029-03-15 に47日．
- 公開 CA の証明書は serverAuth 専用になり，mTLS には私設 CA を使う．
- OCSP は任意化・廃止の方向で，CRL と短命証明書に重心が移っている．
- 主要ブラウザ（Chrome など）は CT ログへの記録（SCT）を信頼の条件にしている．
- ACME 自動化が前提の時代になった．

## 関連項目

- [[HTTP・HTTPS]]
- [[SSH]]
- [[ネットワーク]]
- [[ゼロトラストセキュリティ]]
- [[SSO]]
- [[Let's Encrypt]]
- [[DNS]]

## 参考

- [RFC 5280 Internet X.509 PKI Certificate and CRL Profile](https://datatracker.ietf.org/doc/html/rfc5280)
- [RFC 6960 OCSP](https://datatracker.ietf.org/doc/html/rfc6960)
- [RFC 8555 ACME](https://datatracker.ietf.org/doc/html/rfc8555)
- [CA/Browser Forum Ballot SC081v3](https://cabforum.org/2025/04/11/ballot-sc081v3-introduce-schedule-of-reducing-validity-and-data-reuse-periods/)
- [Baseline Requirements SC081v3 redlined (PDF)](https://cabforum.org/2025/04/11/ballot-sc081v3-introduce-schedule-of-reducing-validity-and-data-reuse-periods/BR-SC081v3-redlined.pdf)
- [CA/Browser Forum Ballot SC063v4](https://cabforum.org/2023/07/14/ballot-sc063v4-make-ocsp-optional-require-crls-and-incentivize-automation/)
- [Let's Encrypt: OCSP Service Has Reached End of Life](https://letsencrypt.org/2025/08/06/ocsp-service-has-reached-end-of-life)
- [Let's Encrypt: 6-day and IP address certificates GA](https://letsencrypt.org/2026/01/15/6day-and-ip-general-availability)
- [Let's Encrypt: Profiles](https://letsencrypt.org/docs/profiles/)
- [Chrome Root Program Policy](https://googlechrome.github.io/chromerootprogram/crp/policy/)
- [Let's Encrypt: From 90 to 45](https://letsencrypt.org/2025/12/02/from-90-to-45)
- [Chrome Certificate Transparency Policy](https://googlechrome.github.io/CertificateTransparency/ct_policy.html)
- [MDN: Certificate Transparency](https://developer.mozilla.org/en-US/docs/Web/Security/Defenses/Certificate_Transparency)
