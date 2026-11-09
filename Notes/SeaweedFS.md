---
title: "SeaweedFS"
date: 2026-08-02
updated: 2026-11-09
tags:
  - Cloud
  - DB
related:
  - "[[オブジェクトストレージ]]"
  - "[[MinIO]]"
  - "[[Apache Iceberg]]"
  - "[[Go製CLIツール]]"
  - "[[RustFS]]"
  - "[[Garage]]"
  - "[[Ceph]]"
  - "[[AIStor]]"
---

## 概要

SeaweedFS は，Facebook の Haystack 論文の設計思想を基にした OSS（Apache-2.0）の分散ストレージシステム．小さなファイルへの O(1) ディスクアクセスを実現し，数十億ファイル規模を効果的に扱える点を特徴とする．単一の `weed` バイナリで，S3 互換の[[オブジェクトストレージ]]，POSIX ライクなファイルシステム，[[Apache Iceberg]] を扱う S3 Tables（レイクハウス）までカバーする．

## 詳細

### アーキテクチャ（Haystack ベース）

SeaweedFS は，ファイルを個別のオブジェクトとしてディスクに保存するのではなく，複数のファイルを「Volume」というまとまりにパッキングして格納する．これにより，膨大な数の小ファイルを扱う際にメタデータ管理のオーバーヘッドを大幅に削減し，一定時間でのディスクシークを実現する．

### コンポーネント構成

| コンポーネント | 役割 |
|--------------|------|
| **Master Server** | メタデータ（Volume の配置情報等）を管理．メタデータ自体は非常に小さく保たれる |
| **Volume Server** | 実データを保持．複数ファイルを Volume にパッキングして格納 |
| **Filer Server** | ファイルシステムのようなディレクトリ・パス階層のセマンティクスを付与 |
| **S3 Gateway** | S3 互換 REST API を公開し，既存の S3 クライアントからアクセス可能に |

各コンポーネントは独立してスケール可能で，メタデータ管理とデータ保存を分離することで巨大なファイル数でもメモリオーバーヘッドを抑える設計になっている．

### 提供するストレージ層

- **Blob Storage**: Master/Volume Server とクラウド階層からなる基盤層．ほぼ無制限にスケール可能
- **File Storage**: Blob Storage の上に Filer を重ね，ファイルシステムライクな操作（ディレクトリ，パス）を提供
- **Object Storage（S3 API）**: S3 Gateway 経由でオブジェクトストレージとして利用
- **S3 Tables / Iceberg REST Catalog**: S3 Table Bucket に [[Apache Iceberg]] テーブル（Lance テーブルも）を格納し，組み込みの Iceberg REST Catalog で提供する．別途 Hive Metastore や Glue は不要．カタログは S3 API とは別のポートで待ち受け，コミットはアトミックな compare-and-swap で並行書き込みに対応する．Spark，Trino，DuckDB，Dremio，ClickHouse，LanceDB などが対応エンジンとして挙げられている

### データ保護・運用機能

- レプリケーション，ウォームデータ向けのイレイジャーコーディング（EC），クラウド階層化に対応
- 保存時暗号化，TLS/mTLS，FIPS ビルド
- FUSE マウント，WebDAV，SFTP，HDFS，Kubernetes 連携（CSI ドライバ，Operator）
- Admin UI，Prometheus メトリクス，多様な Filer メタデータストア
- Iceberg テーブルの自動メンテナンス（コンパクション，スナップショット期限切れ，孤立ファイル削除，マニフェスト書き換え）

### 最近の動向（2026-11-09 時点）

- 4.x 系のリリースが短い間隔で続いており，最新は 4.53（2026-11-08）．S3 のバケット単位の Block Public Access，S3 Vectors バケット向けタグ条件ポリシー，EC の破損シャード再構築などが入っている
- 4.52 で S3 Vectors API ゲートウェイが追加された
- 4.47〜4.50 では `aws:SourceIp` の評価（直接の TCP ピア基準，信頼プロキシの許可リスト），Object Lock 権限の分離，バケットポリシー条件の拡充など，S3 のセキュリティ周りの強化が続く
- 4.49 では IAM/OIDC 関連で，再利用された JWT の jti を拒否するリプレイキャッシュが追加された
- README は MinIO について「2026-04-25 に開発終了」と記し，利用を強く非推奨としている（プロジェクト自身の評価）．代替として Apache-2.0 の Rust 実装 [[RustFS]] にも言及している
- 別途 Enterprise Edition があり，データ復旧，自己修復ストレージ，カスタマイズ可能な EC，EC の vacuum/repair などが有償機能とされる

### 特徴・利点

- 数十億ファイル規模でも O(1) のディスクアクセスを維持
- [[MinIO]] や [[Ceph]] と比べてメモリフットプリントが小さい（プロジェクトの主張）
- FUSE マウントによる POSIX ライクなファイルシステムアクセスにも対応
- S3・ファイルシステム・Iceberg カタログを単一システムで統合的に提供

## ポイント

- Facebook Haystack 論文由来の「小ファイルを Volume にパッキング」する設計が核
- Master/Volume/Filer/S3 Gateway の疎結合な役割分担でスケーラビリティを確保
- MinIO・Ceph と比べて軽量（低メモリ）であることを謳う
- S3 互換オブジェクトストレージに加え，ファイルシステムや Iceberg テーブルストレージ（S3 Tables）としても使える汎用性
- 4.x 系は S3 互換性・セキュリティ・EC の改善が高頻度に入るため，運用時は最新リリースノートの確認が必要

## 関連項目

- [[オブジェクトストレージ]] — SeaweedFS が S3 Gateway 経由で提供するインターフェース
- [[MinIO]] — 同じくセルフホスト可能な S3 互換ストレージの代表格
- [[Apache Iceberg]] — SeaweedFS が REST Catalog として対応するテーブルフォーマット
- [[RustFS]] — Rust 製の MinIO 互換 OSS ストレージ
- [[Garage]] — 軽量なセルフホスト向け S3 互換ストレージ
- [[Ceph]] — 比較対象の分散ストレージ
- [[AIStor]]

## 参考

- [SeaweedFS 公式サイト](https://seaweedfs.org/)
- [GitHub - seaweedfs/seaweedfs](https://github.com/seaweedfs/seaweedfs)
- [SeaweedFS Releases](https://github.com/seaweedfs/seaweedfs/releases)
- [SeaweedFS 4.53 リリース](https://github.com/seaweedfs/seaweedfs/releases/tag/4.53)

## 更新履歴

- 2026-11-09: Apache-2.0，S3 Tables/Iceberg REST Catalog の詳細，運用機能，4.47〜4.53 の動向，MinIO 開発終了に関する README の記述，Enterprise Edition を追記
