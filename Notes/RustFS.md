---
title: "RustFS"
date: 2026-08-02
updated: 2026-11-09
tags:
  - Cloud
  - DB
related:
  - "[[オブジェクトストレージ]]"
  - "[[MinIO]]"
  - "[[Rust]]"
  - "[[Amazon S3]]"
  - "[[AIStor]]"
  - "[[Garage]]"
  - "[[SeaweedFS]]"
---

## 概要

RustFS は，[[Rust]]で実装された S3 互換の高性能[[オブジェクトストレージ]]．MinIO の代替として位置づけられ，4KB のような小さいオブジェクトのペイロードで MinIO の 2.3 倍のスループットを謳う（2026-11-09 時点の公式サイト・README・GA 告知には数値の記載がなく，出典と独立検証は未確認）．AI ワークロード（ベクトルDB・分析基盤との連携）を主要ユースケースの一つとして掲げ，Apache 2.0 ライセンスで公開されている．2026-09-16 に 1.0.0 が GA となり，2026-11-09 時点の最新は 1.0.4（2026-10-25）．

## 詳細

### MinIO との関係と位置づけ

RustFS は MinIO・Ceph 等の既存 S3 互換プラットフォームとの移行・共存をサポートすることを明言しており，MinIO からの乗り換え先として設計されている．MinIO は AGPL v3 ライセンスで公開されていたが，GitHub リポジトリは 2026-04-25 にアーカイブされて読み取り専用となり，「THIS REPOSITORY IS NO LONGER MAINTAINED」と表示され，後継として [[AIStor]]（Free / Enterprise）が案内されている．コミュニティ版はソースコードのみの配布で，事前ビルド済みバイナリは提供されない．RustFS は Apache 2.0 ライセンスを採用することで AGPL の制約を避けている．

### リリース状況（2026-11-09 時点）

| バージョン | 日付 | 内容 |
|-----------|------|------|
| 1.0.0-rc.4〜rc.6 | 2026-08-28〜09-11 | 性能ワークフロー，オンデマンド移行，tiering/ILM 修正など |
| 1.0.0 | 2026-09-16 | 初の非プレリリース（GA）．heal/MRF 復旧，スキャナ，SSE/KMS を重点的に修正 |
| 1.0.1 | 2026-10-03 | nightly パッケージ，コンソール URL プレフィックス設定 |
| 1.0.2 | 2026-10-16 | opt-in の HTTP/3（QUIC），クォータ予約プロトコル，Kafka SASL，Object Lock 残存保持日数など |
| 1.0.3 | 2026-10-17 | 組み込みコンソール更新（v0.1.37） |
| 1.0.4 | 2026-10-25 | 依存関係更新，証明書検証・ルート CA の更新 |

GA 告知は「コアのオブジェクトストレージエンジンは安定しており本番利用可能」と述べるにとどまり，全機能が本番水準とは言っていない．ロードマップには S3 Vectors 対応や AI 中心の「RustFS 2.0」が挙がっている（未提供）．公式サイトによればネイティブ RDMA と DPU へのオフロードは開発中．

### 技術的特徴

- **Rust による実装**: メモリ安全性を保ちながら高いパフォーマンスを実現．GC がない点を MinIO（Go）との差として強調
- **S3 互換 API**: AWS Signature Version 4（SigV4）に対応し，AWS CLI や各種 S3 SDK からそのまま利用可能
- **IAM 互換のアクセス制御**: ポリシーベースのアクセス制御に加え，OIDC/SSO，STS，KMS，サーバーサイド暗号化，監査ログ，mTLS をサポート
- **データ保護・運用**: Reed-Solomon イレージャーコーディング，ビットロット保護，ヒーリング，バージョニング，Object Lock（WORM），ライフサイクル，リモート S3 への ILM tiering，バケット/サイト間レプリケーション
- **デプロイ形態**: 単一ノードと分散モード．プール拡張，リバランス，プール廃止，Helm チャートに対応
- **追加プロトコル**: Swift API，FTPS，WebDAV，SFTP（opt-in），MCP
- **S3 Tables**: Apache Iceberg REST Catalog を内蔵（README では Preview 扱い）
- **MinIO ディスク互換**: `rio-v2` feature 配下の Preview で既定ビルドには含まれない．MinIO で暗号化されたオブジェクトは RustFS では読めない

### 想定ユースケース

- コードリポジトリのオブジェクトストレージ（GitHub/GitLab のようなワークロード）
- 分析基盤（MongoDB，ClickHouse，MariaDB，CockroachDB，Teradata 等との組み合わせ，Iceberg 連携）
- バックアップ・アーカイブ
- AI/ML 向けの大規模データストレージ（[[ベクトルDB]] のバックエンドとしての評価事例あり，例: Milvus）

### ライセンスとエコシステム

Apache 2.0 ライセンスにより，AGPL のような「派生物の公開義務」を回避できる点が企業導入における訴求点になっている．MinIO や Ceph からのデータ移行機能を備え，既存 S3 互換エコシステムからの乗り換えを容易にしている．なお MinIO からの移行ガイドにはバージョン・トポロジの制約があるため，本番データの移行前に確認と検証が必要．

## ポイント

- Rust 製の S3 互換オブジェクトストレージで，MinIO の直接的な代替として設計
- 2026-09-16 に 1.0.0 GA，最新は 1.0.4（2026-10-25）．ただし GA の保証範囲はコアエンジン
- 小オブジェクト（4KB）で MinIO 比 2.3 倍のスループットを謳うが，一次情報では数値を確認できず自社ベンチマーク由来
- Apache 2.0 ライセンスにより MinIO の AGPL 制約を回避．MinIO 本体は 2026-04 にアーカイブ済み
- AI/ML ワークロード（ベクトルDB連携等）を意識したポジショニング
- クイックスタートの既定認証情報（`rustfsadmin`）は本番前に必ず変更する

## 関連項目

- [[オブジェクトストレージ]] — RustFS が実装するストレージ方式
- [[MinIO]] — RustFS が直接の代替として位置づける既存ツール（アーカイブ済み）
- [[AIStor]] — MinIO 後継として案内される商用製品
- [[Garage]] / [[SeaweedFS]] — 他の自己ホスト型 S3 互換ストレージ
- [[Rust]] — RustFS の実装言語
- [[Amazon S3]] — RustFS が互換性を提供する API の標準

## 参考

- [RustFS 公式サイト](https://rustfs.com/)
- [GitHub - rustfs/rustfs](https://github.com/rustfs/rustfs)
- [RustFS Releases](https://github.com/rustfs/rustfs/releases)
- [Announcing RustFS 1.0.0 GA](https://rustfs.com/blog/announcing-rustfs-1-0-0-ga)
- [RustFS Documentation](https://docs.rustfs.com/)
- [GitHub - minio/minio（アーカイブ済み）](https://github.com/minio/minio)

## 更新履歴

- 2026-11-09: 1.0 GA と 1.0.x リリース状況，MinIO リポジトリのアーカイブ，機能一覧（Preview 含む）を追記．2.3 倍の主張を未検証と明記．リンク切れの S3 互換性ドキュメントを差し替え
