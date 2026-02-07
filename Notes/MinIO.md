---
title: "MinIO"
date: 2026-02-07
tags:
  - Cloud
  - Tool
related:
  - "[[オブジェクトストレージ]]"
  - "[[データレイク]]"
  - "[[データ基盤]]"
---

## 概要

MinIO は Go 言語で実装された高性能な Amazon S3 互換[[オブジェクトストレージ]]サーバーである。シングルバイナリで配布され、オンプレミス・プライベートクラウド・エッジ環境など、あらゆる環境に展開可能。軽量で導入が容易でありながら、エンタープライズ級のスケーラビリティとパフォーマンスを提供する。

## 詳細

### 主な特徴

| 特徴 | 説明 |
|------|------|
| **S3 互換 API** | Amazon S3 と完全互換。AWS CLI や AWS SDK からそのままアクセス可能。S3 を前提としたアプリケーションを変更なしで利用できる |
| **高性能** | 並列処理と最適化された I/O により高速なデータ転送を実現。AIStor（商用版）では 2.2 TiB/s 超のスループットを達成 |
| **軽量設計** | シングルバイナリで配布。Node.js や Redis のようにアプリケーションスタックにバンドル可能 |
| **水平スケーリング** | サーバープール方式で水平スケール可能。Kubernetes 上での展開にも対応 |
| **イレージャーコーディング** | データの冗長性と耐障害性を確保。ノード障害時のデータ復旧が可能 |
| **暗号化** | サーバーサイド暗号化（SSE-S3、SSE-C）と転送中暗号化（TLS）をサポート |

### メリット

- **導入の容易さ**: インストールから起動まで数分で完了。Docker / Kubernetes でも簡単にデプロイ可能
- **S3 互換性**: 本番環境では Amazon S3、開発・テスト環境では MinIO を使うなど、環境を使い分けられる
- **コスト削減**: オンプレミスに展開することで、クラウドストレージのデータエグレス料金やストレージ料金を削減できる
- **データ主権**: データを自社のインフラ内に保持でき、コンプライアンス要件への対応が容易
- **マルチクラウド対応**: パブリッククラウド、プライベートクラウド、エッジのいずれにも展開可能

### ライセンス

MinIO はデュアルライセンス方式を採用している:

| ライセンス | 対象 | 条件 |
|-----------|------|------|
| **GNU AGPLv3** | Community Edition（OSS） | ネットワーク経由で MinIO ソフトウェアを配布・ホスト・派生物を作成する場合、結合した作品の完全なソースコードも AGPLv3 で公開する義務がある |
| **商用ライセンス** | AIStor Enterprise Edition | AGPLv3 の義務を免除。ミッションクリティカルな環境向けに SLA を提供。年間 $96,000〜（400 TB まで） |

#### 2025年の重要な変更

2025年3月、MinIO は Community Edition（OSS 版）から Web 管理 UI の主要機能を削除した:

- **削除された機能**: ポリシー管理、リアルタイム監視、レプリケーション制御、バケット管理、設定管理、ライフサイクル管理
- **残された機能**: オブジェクトブラウザのみ。コマンドライン（`mc`）による管理は引き続き可能
- **管理 UI の復元**: AIStor Enterprise Edition への移行が必要

この変更はコミュニティから大きな反発を受け、コミュニティ主導のフォーク「**OpenMaxIO**」が誕生した。

### 代替ツール

| ツール | 特徴 | 適するユースケース |
|--------|------|-------------------|
| **Ceph RGW** | Ceph エコシステムの一部として動作する S3 互換ゲートウェイ。高度な配置ポリシーとマルチサイトレプリケーション。マルチテナンシーに優れる。リソース要件が大きい（OSD あたり 8〜16 GB RAM） | 大規模エンタープライズ、OpenStack 環境、高度なマルチテナンシー要件 |
| **SeaweedFS** | Facebook の Haystack に着想を得たマスター・ボリューム方式。メタデータとデータストレージを分離。軽量（ボリュームサーバーあたり 2〜4 GB RAM）。POSIX FUSE マウント対応 | 大量の小ファイル、アーカイブ・バックアップ、マルチアクセスパターン |
| **Garage** | Rust 製の軽量オブジェクトストレージ。強い整合性保証。地理分散に対応。最小限のリソースで動作 | エッジ環境、小規模クラスタ、地理分散 |
| **RustFS** | Rust 製の MinIO 代替。MinIO からのフォーク |  MinIO からの移行先候補 |
| **OpenMaxIO** | MinIO のコミュニティフォーク。Web UI 機能を維持 | MinIO OSS 版の管理 UI が必要な場合 |

#### 代替ツール選定の指針

- **S3 互換性重視**: MinIO（最も包括的な S3 互換性）
- **大規模エンタープライズ**: Ceph RGW（成熟した運用実績、高度なマルチテナンシー）
- **軽量・シンプルさ重視**: SeaweedFS または Garage
- **コスト重視 / AGPLv3 回避**: Ceph RGW（LGPL）、SeaweedFS（Apache 2.0）

### ユースケース

- **開発・テスト環境**: 本番の S3 を模したローカル環境の構築
- **AI/ML データレイク**: エッジからデータを収集し、中央の[[データレイク]]に送信。AI モデルトレーニングの基盤として機能
- **バックアップ・アーカイブ**: オンプレミスでの大容量データのバックアップ先
- **プライベートクラウド**: データ主権やコンプライアンス要件のためにデータを自社管理
- **Kubernetes ネイティブストレージ**: コンテナ環境における永続ストレージ

## ポイント

- S3 完全互換のオープンソースオブジェクトストレージで、あらゆる環境に展開可能
- AGPLv3 のデュアルライセンス。ネットワーク経由での提供にはソースコード公開義務がある点に注意
- 2025年に OSS 版から Web 管理 UI が削除され、コミュニティフォーク（OpenMaxIO）が誕生
- 代替ツールとして Ceph RGW（大規模向け）、SeaweedFS（軽量）、Garage（エッジ向け）がある
- 開発環境の S3 エミュレーションから AI/ML のデータレイクまで幅広いユースケースに対応

## 関連項目

- [[オブジェクトストレージ]]
- [[データレイク]]
- [[データ基盤]]

## 参考

- [MinIO GitHub](https://github.com/minio/minio)
- [MinIO - 商用ライセンス](https://www.min.io/commercial-license)
- [MinIO Blog - From Open Source to Free and Open Source](https://blog.min.io/from-open-source-to-free-and-open-source-minio-is-now-fully-licensed-under-gnu-agplv3/)
- [OpenStandia - MinIOとは？](https://openstandia.jp/oss_info/minio/)
- [onidel.com - MinIO vs Ceph RGW vs SeaweedFS vs Garage in 2025](https://onidel.com/blog/minio-ceph-seaweedfs-garage-2025)
- [Futuriom - MinIO Faces Fallout for Stripping Functions from Open Source Version](https://www.futuriom.com/articles/news/minio-faces-fallout-for-stripping-features-from-web-gui/2025/06)
