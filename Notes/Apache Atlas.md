---
title: "Apache Atlas"
date: 2026-04-13
tags:
  - DB
  - Cloud
related:
  - "[[データカタログ]]"
  - "[[データリネージ]]"
  - "[[データ品質]]"
  - "[[Apache Hadoop]]"
  - "[[Apache Hive]]"
  - "[[Apache Kafka]]"
  - "[[DMBOK]]"
---

## 概要

Apache Atlas は Apache Software Foundation が管理するオープンソースのメタデータ管理・データガバナンスフレームワーク。データ資産のカタログ化、データリネージの追跡、分類ベースのセキュリティポリシー適用を提供し、Hadoop エコシステムを中心としたエンタープライズデータ基盤のガバナンスを支援する。

## 詳細

### アーキテクチャ

Apache Atlas は以下のコンポーネントで構成される：

| コンポーネント | 役割 |
|---------------|------|
| **Type System** | メタデータのモデル（型）とインスタンス（エンティティ）を定義・管理 |
| **Graph Engine** | メタデータオブジェクト間の関係をグラフモデルで永続化（JanusGraph） |
| **Graph Store** | メタデータの永続化ストア（HBase、PostgreSQL） |
| **Index Store** | 全文検索・属性検索を提供（Apache Solr） |
| **Ingest/Export** | メタデータの取り込みとエクスポート。変更時にイベントを発行 |

外部システムとの連携は **REST API** と **Kafka ベースのメッセージングインターフェース** の2経路で行う。

### 型とエンティティモデル

Atlas のメタデータ管理は「Type（型）」と「Entity（エンティティ）」の2層構造：

- **Type**: メタデータの構造を定義するスキーマ（例: `hive_table`、`hive_column`）
- **Entity**: Type のインスタンス（例: 特定のテーブル `sales_data`）
- カスタム Type を定義してビジネス固有のメタデータモデルを構築可能

### コア機能

#### 1. メタデータ管理

データ資産（テーブル、カラム、プロセス等）をエンティティとして登録し、タグ・分類・ビジネス用語を付与して一元管理する。ビジネスグロッサリー機能により、技術的なメタデータとビジネス用語を紐づけ、データの発見性を向上させる。

#### 2. データリネージ

データの流れ（ソース → 変換 → 出力先）をカラムレベルで自動追跡する。[[データリネージ]]の可視化により：

- データの出自（provenance）の確認
- 変更の影響範囲分析（Impact Analysis）
- コンプライアンス監査の支援

#### 3. 分類とセキュリティ

データに分類タグ（PII、機密、公開等）を付与し、Apache Ranger と連携してアクセスポリシーを自動適用する。分類の伝播機能により、上流で付与した分類が下流のデータにも自動継承される。

#### 4. 検索とディスカバリ

Apache Solr によるフリーテキスト検索、属性フィルタ、分類ベースの絞り込みでデータ資産を発見できる。[[データカタログ]]としてデータの所在と意味を効率的に検索可能。

### ネイティブ対応データソース

- Apache Hive、HBase、Sqoop、Storm、Kafka
- Hadoop エコシステム外への拡張コネクタも存在

### 最新動向（2025-2026年）

- **Atlas 2.4.0**（2025年1月）: 2年ぶりのリリース。GitHub Actions による CI/CD 改善、マイグレーション/インポートの耐障害性修正
- **Atlas 2.5.0**: Trino エクストラクタと PostgreSQL バックエンドのサポートを追加
- **オープンソースカタログの競争**: DataHub（11k+ GitHub stars）や OpenMetadata が台頭。Atlas は Hadoop 中心のスタックで引き続き有力だが、モダンなデータスタックでは DataHub や OpenMetadata が選択肢に

### 導入のベストプラクティス

- **段階的ロールアウト**: 1つのビジネスドメイン・1つのユースケースから開始し、分類基準とメタデータモデルを確立してから拡大する
- **自動メタデータ収集**: Hook やイベント連携を活用し、パイプラインの変更を自動でカタログに反映する
- **分類体系の事前設計**: 企業のプライバシーポリシー・コンプライアンス要件に基づいて分類カテゴリを定義する

## ポイント

- **Hadoop エコシステムとの深い統合**: Hive、HBase、Kafka 等のメタデータを自動収集。Hadoop ベースのデータ基盤には最適解
- **グラフベースのメタデータモデル**: エンティティ間の複雑な関係を柔軟に表現できるが、学習コストは高い
- **Apache Ranger 連携**: 分類ベースの動的アクセス制御とデータマスキングを実現
- **セルフホスト型**: フルマネージドサービスは存在せず、運用・管理は自前で行う必要がある
- **競合の台頭**: DataHub や OpenMetadata がモダンなデータスタック向けに勢力を拡大しており、新規導入時は比較検討が必要

## 関連項目

- [[データカタログ]] — Apache Atlas はデータカタログの代表的な OSS 実装
- [[データリネージ]] — カラムレベルのリネージ追跡が Atlas のコア機能
- [[データ品質]] — メタデータと分類によるデータ品質管理の基盤
- [[Apache Hadoop]] — Atlas の主要な対象エコシステム
- [[Apache Hive]] — Atlas がネイティブ対応するデータソース
- [[Apache Kafka]] — メタデータイベントの通知基盤として連携
- [[DMBOK]] — データガバナンスの知識体系。Atlas はその実装ツール

## 参考

- [Apache Atlas 公式サイト](https://atlas.apache.org/)
- [GitHub: apache/atlas](https://github.com/apache/atlas)
- [Apache Atlas Architecture](https://atlas.apache.org/1.0.0/Architecture.html)
- [Atlan: What Is Apache Atlas?](https://atlan.com/what-is-apache-atlas/)
- [AWS Blog: Metadata classification, lineage, and discovery using Apache Atlas on Amazon EMR](https://aws.amazon.com/blogs/big-data/metadata-classification-lineage-and-discovery-using-apache-atlas-on-amazon-emr/)
