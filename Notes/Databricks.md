---
title: "Databricks"
date: 2026-02-26
tags:
  - Cloud
  - DB
  - AI
  - ML
related:
  - "[[Delta Lake]]"
  - "[[Apache Spark]]"
  - "[[データレイクハウス]]"
  - "[[MLOps]]"
  - "[[Snowflake]]"
  - "[[dbt]]"
  - "[[メダリオンアーキテクチャ]]"
  - "[[Apache Kafka]]"
---

## 概要

DatabricksはApache Sparkの創設者たちが設立した企業が提供するクラウドベースの統合データ・AIプラットフォーム。データレイクのコスト効率性とデータウェアハウスの高性能・ガバナンスを統合した「レイクハウス」アーキテクチャを実現し、データエンジニアリング・データサイエンス・機械学習・BI を単一プラットフォームで提供する。

## 詳細

### レイクハウスアーキテクチャ

Databricks のコアアーキテクチャは **レイクハウス** 思想に基づく。

```
                    ┌─────────────────────────────────┐
                    │       Databricks Platform        │
      ┌─────────────┼──────────────────────────────────┤
      │ データ格納  │  Object Storage (S3/ADLS/GCS)   │
      │             │  + Delta Lake（信頼性レイヤー）  │
      ├─────────────┼──────────────────────────────────┤
      │ 処理エンジン │  Apache Spark + Photon           │
      ├─────────────┼──────────────────────────────────┤
      │ ガバナンス  │  Unity Catalog                   │
      ├─────────────┼──────────────────────────────────┤
      │ ワークロード │  ETL / SQL / ML / Streaming      │
      └─────────────┴──────────────────────────────────┘
```

### 主要コンポーネント

#### Delta Lake

Databricks が開発したOSSのオープンテーブルフォーマット（現在は Linux Foundation 傘下）。Object Storage 上の Parquet ファイルに対して、ACID トランザクション・スキーマ管理・タイムトラベルを提供する。Databricks のストレージ基盤として機能する。

#### Databricks SQL（旧 SQL Analytics）

Spark 上で動作するサーバレス SQL エンジン。BI ツール（Tableau, Power BI, Looker 等）との接続に対応し、DWH 的な利用を実現。Photon エンジンにより高速なクエリ実行が可能。

#### Unity Catalog

2022年に発表したデータガバナンス統合レイヤー。データ・AI アセット（テーブル、ビュー、モデル、ダッシュボード等）を一元管理し、アクセス制御・リネージ・データ探索を提供する。

#### MLflow

Databricks が開発したOSSのML実験管理ツール（現在は Linux Foundation 傘下）。実験トラッキング・モデル管理・モデルサービングを提供し、Databricks プラットフォームにネイティブ統合されている。

#### Photon

Apache Spark の互換実行エンジンだが、C++ で再実装した高速エンジン。既存の Spark コードをそのまま使いつつ、最大5倍の SQL クエリ性能を実現。

### Databricks vs Snowflake

| 観点 | Databricks | Snowflake |
|------|-----------|-----------|
| 強み | ML/AI・ストリーミング・データエンジニアリング | SQL・BI・データ共有・DWH |
| ストレージ | Delta Lake（OSS） | 独自形式 |
| 処理エンジン | Spark + Photon | 独自エンジン |
| ML 統合 | ネイティブ（MLflow） | 限定的 |
| コスト | 使用量ベース（複雑） | ウェアハウスサイズベース |
| 適合ユースケース | 大規模ETL・ML・ストリーミング | SQL分析・レポーティング |

### データエンジニアリングでの活用

- **ETL/ELT パイプライン**: Spark を使った大規模データ変換。dbt + Databricks の組み合わせも普及
- **ストリーミング処理**: Spark Structured Streaming + Kafka でリアルタイムパイプライン
- **データレイクの管理**: Delta Lake による信頼性の高いデータレイク運用
- **ML パイプライン**: MLflow によるモデルの実験管理から本番デプロイまで
- **Auto Loader**: クラウドストレージへのファイル到着を検出し自動取り込みする機能

### 2025〜2026 のトレンド

- **AI-native Lakehouse**: ベクター検索・RAG・モデル推論をレイクハウスのファーストクラス機能として統合
- **ストリーミングファースト**: リアルタイム処理をデフォルトとする設計への移行
- **DAIS 2025**: Databricks が Delta Lake を越えた「Iceberg との互換性向上」を発表

## ポイント

- **マルチクラウド対応**: AWS, Azure, GCP すべてで稼働し、同一のワークスペースUIで操作できる
- **Cluster の管理**: Cluster のサイズ・タイプ（Standard, ML, GPU等）の選択がコストと性能に直結する
- **サーバレスコンピュート**: 2023年以降、サーバレスモードでクラスタ管理が不要になった
- **Delta Live Tables（DLT）**: パイプラインを宣言的に定義し、品質チェック・自動リトライを組み込める

## 関連項目

- [[Delta Lake]] — Databricks の中核となるオープンテーブルフォーマット
- [[Apache Spark]] — Databricks の処理エンジン
- [[データレイクハウス]] — Databricks が体現するアーキテクチャ概念
- [[MLOps]] — MLflow を中心とした ML ライフサイクル管理
- [[Snowflake]] — DWH・SQL分析特化の主要競合
- [[dbt]] — Databricks 上でのSQL変換ツールとして組み合わせて使われる
- [[Apache Kafka]] — Databricks への取り込みでよく組み合わせるストリーミング基盤

## 参考

- [Databricks: What is Databricks?](https://docs.databricks.com/aws/en/introduction/)
- [Databricks Blog: SQL on Databricks Lakehouse 2025](https://www.databricks.com/blog/sql-databricks-lakehouse-2025)
