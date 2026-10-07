---
title: "Samba"
date: 2026-10-07
tags:
  - Tool
  - Security
related:
  - "[[NAS]]"
  - "[[ファイルストレージ]]"
  - "[[ネットワーク]]"
  - "[[SSH]]"
  - "[[SFTP]]"
  - "[[FTP]]"
  - "[[SCP]]"
  - "[[DNS]]"
  - "[[NFS]]"
  - "[[Active Directory]]"
---

## 概要

Samba は Windows のファイル共有プロトコルである SMB/CIFS を Unix 系 OS 上で実装したオープンソースソフトウェア群．Linux などをファイル・プリントサーバとして動作させられ，Samba 4 以降は Active Directory ドメインコントローラ（AD DC）としても動作する．2026-10-07 時点の最新安定版は 4.25.0（2026-09-24 リリース）．

## 詳細

### 歴史

- Andrew Tridgell がパケットスニファで SMB プロトコルをリバースエンジニアリングし，Unix 上に実装して 1992 年初頭にコードを公開した．当初の名称が商標上の問題を指摘され，スペルチェック用辞書から "smb" を含む単語を探して "Samba" と名付けた（Samba 公式の紹介文書）．
- Samba 3.0.0（2003-09-24）で，AD（ADS レルム）にメンバサーバとして参加し，LDAP / Kerberos でユーザを認証できるようになった．
- Samba 4.0（2012-12-11）で AD のログオン環境のサーバ側（AD DC）に対応した．内蔵の LDAP サーバと Kerberos KDC を持つ．
- 4.11.0（2019-09-17）で `client min protocol` / `server min protocol` の既定値が SMB2_02 に変更され，SMB1 のみ対応のクライアントは既定では接続できなくなった．

### SMB のバージョンとセキュリティ

- SMB1（CIFS）: 重大な脆弱性があり，Microsoft は使用しないよう強く推奨している．Windows 11 の全エディションと Windows Server 2019 以降では既定で未インストール．Windows 10 については，同じ Microsoft Learn のページ内に「Home と Pro を除き既定で未インストール」と「Fall Creators Update（1709）以降は既定で未インストール」という記述が併存しており，エディションによって扱いが異なる．
- SMB2: Windows Vista / Server 2008 で導入．署名が MD5 から HMAC-SHA256 になり，リクエストのコンパウンド，大きな読み書き，durable handle などが加わった．
- SMB3: Windows 8 / Server 2012 で導入．透過的フェイルオーバー，マルチチャネル，SMB Direct（RDMA），暗号化，ディレクトリリースなど．SMB 3.0/3.02 の署名は AES-CMAC．
- SMB 3.1.1: 事前認証整合性（Negotiate と Session Setup のハッシュで改ざん・ダウングレードを検知）が必須．ただし SMB1 へのダウングレードは防げないため，SMB1 サーバの無効化が必要．Windows Server 2022 / Windows 11 で AES-256-GCM/CCM 暗号化と AES-128-GMAC 署名が追加された．
- Windows 11 24H2 / Windows Server 2025 では SMB 署名が既定で必須となり，署名未対応のサードパーティ SMB サーバやゲスト共有に接続できない場合がある．回避のためにバージョンや署名を無効化せず，サーバ側で署名を有効化することが推奨されている．
- Samba 側の既定値（smb.conf マニュアル）: `server min protocol = SMB2_02`，`client min protocol = SMB2_02`，`server signing = default`，`smb encrypt = default`，`security = user`，`map to guest = never`．
- Samba 4.23（2025-09-12）で SMB3 over QUIC をサポート（例: `server smb transports = +quic`．サーバ側は外部の quic.ko カーネルモジュールが必要で，Linux 6.14 で検証されている．クライアントはモジュールがなければユーザ空間の ngtcp2 にフォールバックする）．SMB3 UNIX Extensions が既定で有効になった．

### ファイル・プリンタ共有と smb.conf

- 主要デーモン: `smbd`（ファイル・プリント共有，認証）と `nmbd`（名前解決，ブラウジング）．AD DC 構成では `samba` デーモンが担う（既存知識）．
- 基本設定の例（既存知識に基づく最小例．実運用前に公式マニュアルで確認すること）:

```ini
[global]
   workgroup = WORKGROUP
   server role = standalone server
   server min protocol = SMB2_02
   server signing = mandatory
   map to guest = never

[share]
   path = /srv/share
   read only = no
   valid users = alice
```

- ユーザは `smbpasswd -a <user>` で Samba 用パスワードを登録する（既存知識）．`testparm` で設定を検証する（既存知識）．
- `valid users` の既定は空で，全ユーザが許可される（マニュアル）．

### Active Directory ドメインコントローラ機能

- `samba-tool domain provision` で AD データベースと初期レコード（管理者アカウント，必要な DNS エントリ）を作成する．
- フォレスト機能レベルは Windows Server 2008 R2 相当．AD バックエンドは内蔵 LDAP のみ，KDC は Heimdal Kerberos．
- DNS バックエンドは `SAMBA_INTERNAL`（最初の DC に推奨）と `BIND9_DLZ`．[[DNS]] 設計が重要で，AD の DNS ゾーンは改名不可，`.local` は Avahi と衝突するため避ける，ホスト名は 15 文字未満，DC は静的 IP，フェイルオーバーのため 2 台以上の DC を推奨，複数 DC 環境で DC をファイルサーバにしない，と公式 wiki に記載がある．
- 追加の DC は provision ではなく join する．

### macOS / Windows / Linux からの接続

- macOS: Finder で「移動」>「サーバへ接続」からアドレスを入力して接続（例 `smb://host/share`）．macOS 向けには `vfs_fruit`（`vfs_catia`，`vfs_streams_xattr` と併用）で Apple クライアントとの互換性を高められる．マニュアルの例は `fruit:resource = file`，`fruit:metadata = netatalk` など．
- Windows: エクスプローラで `\\host\share`．SMB1 を有効化して古い Samba に繋ぐのは非推奨．
- Linux: `mount -t cifs //host/share /mnt -o username=...`．`vers=` で方言を指定でき，現在のカーネルの既定は SMB2.1 以降．

### NAS での利用

多くの家庭用・業務用 [[NAS]] が Samba を内蔵し，SMB 共有を提供している（既存知識）．NAS 側の SMB 最小バージョン・署名設定が古いと，Windows 11 24H2 等から接続できない場合がある．

### 代表的な脆弱性

- SambaCry（CVE-2017-7494）: 3.5.0 以降の全バージョンに影響．書き込み可能な共有に悪意あるクライアントが共有ライブラリをアップロードし，サーバにロード・実行させられるリモートコード実行．修正版は 4.6.4，4.5.10，4.4.14．回避策は `[global]` に `nt pipe support = no`（Windows クライアントの機能に影響しうる）．
- 2026-07-28 に 4.24.5，4.23.10，4.22.11 のセキュリティリリースが出て，CVE-2026-6949 ほか計 6 件が修正された（各 CVE の内容と深刻度は未確認）．
- 4.25.0 では，ドメイン機能レベル 2008 以上で `kdc default domain supported enctypes` の既定値が AES（aes128 / aes256-cts-hmac-sha1-96）になった．CVE-2026-20833 への対処とされている．

### 最新バージョン（2026-10-07 時点，samba.org）

| 系列 | 最新版 | 日付 |
|---|---|---|
| 4.25 | 4.25.0 | 2026-09-24 |
| 4.24 | 4.24.7 | 2026-09-09 |
| 4.23 | 4.23.13 | 2026-10-01 |

4.25.0 の主な変更: SMB3 Persistent Handles（実験的．透過的フェイルオーバーの基盤．kernel oplocks / kernel share modes / POSIX ロックを無効にする必要があり，ローカルアクセスや NFS とは併用できない．ハンドル情報を同期的に永続化するため遅延が増える），クラスタ全体の速度制限（`vfs_aio_ratelimit` と `ratelimitd`），Ceph RGW 用 `vfs_ceph_rgw`，CTDB のクラスタ機能レベル．`getwd cache` パラメータは削除．

### NFS との比較（既存知識）

| 観点 | SMB（Samba） | NFS |
|---|---|---|
| 主な対象 | Windows，macOS，混在環境 | Unix 系同士 |
| 認証 | ユーザ認証（NTLM，Kerberos，AD 連携） | 伝統的には UID ベース，NFSv4 では Kerberos |
| 機能 | ACL，プリント共有，AD 連携 | シンプルで POSIX 親和性が高い |
| 用途 | オフィス共有，NAS | サーバ間共有，Linux クラスタ |

## ポイント

- SMB1 は使わない．Samba 4.11 以降は既定で SMB2_02 未満を拒否する．
- 署名と暗号化（`server signing`，`smb encrypt`）を要件に応じて有効化する．暗号化は性能コストがある．
- Windows 11 24H2 以降は署名が既定で必須．古い NAS や Samba 設定では接続失敗に注意．
- 書き込み可能な共有と古い Samba の組み合わせは SambaCry のような RCE リスクがある．セキュリティリリースを追従する．
- AD DC として使う場合は DNS・時刻・DC 台数の設計が重要．DC をファイルサーバ兼用にしない．
- Samba の共有はインターネットに直接公開せず，VPN 等の内側で運用する（既存知識）．

## 関連項目

- [[NAS]]
- [[ファイルストレージ]]
- [[ネットワーク]]
- [[SSH]]
- [[SFTP]]
- [[FTP]]
- [[SCP]]
- [[DNS]]
- [[NFS]]
- [[Active Directory]]

## 参考

- [Samba - Opening Windows to a Wider World（最新リリース）](https://www.samba.org/samba/latest_news.html)
- [Samba Release History](https://www.samba.org/samba/history/)
- [Samba 4.25.0 Release Notes](https://www.samba.org/samba/history/samba-4.25.0.html)
- [Samba 4.23.0 Release Notes](https://www.samba.org/samba/history/samba-4.23.0.html)
- [Samba 4.0.0 Release Notes](https://www.samba.org/samba/history/samba-4.0.0.html)
- [Samba 3.0.0 Release Notes](https://www.samba.org/samba/history/samba-3.0.0.html)
- [Samba 4.11.0 Release Notes](https://www.samba.org/samba/history/samba-4.11.0.html)
- [CVE-2017-7494 Security Advisory](https://www.samba.org/samba/security/CVE-2017-7494.html)
- [smb.conf(5)](https://www.samba.org/samba/docs/current/man-html/smb.conf.5.html)
- [vfs_fruit(8)](https://www.samba.org/samba/docs/current/man-html/vfs_fruit.8.html)
- [An Introduction to Samba](https://www.samba.org/samba/docs/SambaIntro.html)
- [Setting up Samba as an Active Directory Domain Controller（Samba Wiki）](https://wiki.samba.org/index.php/Setting_up_Samba_as_an_Active_Directory_Domain_Controller)
- [Detect, enable, and disable SMBv1, SMBv2, and SMBv3 in Windows（Microsoft Learn）](https://learn.microsoft.com/en-us/windows-server/storage/file-server/troubleshoot/detect-enable-and-disable-smbv1-v2-v3)
- [SMB Security Enhancements（Microsoft Learn）](https://learn.microsoft.com/en-us/windows-server/storage/file-server/smb-security)
- [CIFS Client Usage（Linux kernel docs）](https://www.kernel.org/doc/html/latest/admin-guide/cifs/usage.html)
- [Connect to shared computers and servers（Apple サポート）](https://support.apple.com/guide/mac-help/connect-mac-shared-computers-servers-mchlp1140/mac)
