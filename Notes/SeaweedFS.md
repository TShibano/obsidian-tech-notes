---
title: "SeaweedFS"
date: 2026-08-02
tags:
  - Cloud
  - DB
related:
  - "[[オブジェクトストレージ]]"
  - "[[MinIO]]"
  - "[[Apache Iceberg]]"
  - "[[Go製CLIツール]]"
---

## 概要

SeaweedFS は，Facebook の Haystack 論文の設計思想を基にした OSS の分散ストレージシステム．小さなファイルへの O(1) ディスクアクセスを実現し，数十億ファイル規模を効果的に扱える点を特徴とする．S3 互換の[[オブジェクトストレージ]]，POSIX ライクなファイルシステム，Apache Iceberg のテーブルストレージまで単一システムでカバーする．

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
- **Iceberg REST Catalog**: [[Apache Iceberg]] テーブルのストレージバックエンドとしても利用可能

### 特徴・利点

- 数十億ファイル規模でも O(1) のディスクアクセスを維持
- [[MinIO]] や [[Ceph]] と比べてメモリフットプリントが小さい
- FUSE マウントによる POSIX ライクなファイルシステムアクセスにも対応
- S3・ファイルシステム・Iceberg カタログを単一システムで統合的に提供

## ポイント

- Facebook Haystack 論文由来の「小ファイルを Volume にパッキング」する設計が核
- Master/Volume/Filer/S3 Gateway の疎結合な役割分担でスケーラビリティを確保
- MinIO・Ceph と比べて軽量（低メモリ）であることを謳う
- S3 互換オブジェクトストレージに加え，ファイルシステムや Iceberg テーブルストレージとしても使える汎用性

## 関連項目

- [[オブジェクトストレージ]] — SeaweedFS が S3 Gateway 経由で提供するインターフェース
- [[MinIO]] — 同じくセルフホスト可能な S3 互換ストレージの代表格
- [[Apache Iceberg]] — SeaweedFS が REST Catalog として対応するテーブルフォーマット

## 参考

- [SeaweedFS 公式サイト](https://seaweedfs.org/)
- [GitHub - seaweedfs/seaweedfs](https://github.com/seaweedfs/seaweedfs)
