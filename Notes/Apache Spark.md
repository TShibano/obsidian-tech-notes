---
title: "Apache Spark"
date: 2026-02-10
tags:
  - Cloud
  - DB
related:
  - "[[Apache Hadoop]]"
  - "[[Apache Hive]]"
  - "[[データレイク]]"
  - "[[データレイクハウス]]"
  - "[[データウェアハウス]]"
  - "[[データ基盤]]"
  - "[[Apache Iceberg]]"
  - "[[Apache Parquet]]"
  - "[[MLOps]]"
  - "[[SQL]]"
---

## 概要

Apache Spark は、UC Berkeley の AMPLab で2009年に誕生した大規模データの分散処理エンジンである。インメモリ処理により MapReduce と比較して最大100倍の高速化を実現し、バッチ処理・ストリーミング・機械学習・SQL 分析を単一のフレームワークで統一的に扱える。[[Apache Hadoop]] エコシステムの中核的な処理エンジンとして発展し、現在はデータレイクハウスの標準的な計算エンジンとなっている。

## 詳細

### 歴史

| 年        | 出来事                                                              |
| -------- | ---------------------------------------------------------------- |
| 2009年    | UC Berkeley の AMPLab で Matei Zaharia らが研究プロジェクトとして開始             |
| 2010年    | オープンソースとして公開。RDD に関する論文を発表                                       |
| 2013年    | Apache Software Foundation に寄贈。100名以上のコントリビュータが参加                |
| 2014年2月  | Apache トップレベルプロジェクトに昇格                                           |
| 2014年    | Databricks 社設立（Matei Zaharia らが創業）                               |
| 2016年7月  | Spark 2.0 リリース（DataFrame/Dataset API 統一、Structured Streaming 導入） |
| 2020年6月  | Spark 3.0 リリース（Adaptive Query Execution 等）                       |
| 2025年5月  | Spark 4.0.0 リリース（Spark Connect、ANSI モードデフォルト化）                   |
| 2025年12月 | Spark 4.1.0 リリース（Declarative Pipelines、Real-Time Mode）           |
| 2026年1月  | Spark 4.1.1 リリース（最新安定版）                                          |

### アーキテクチャ

Spark はマスター・スレーブ型のアーキテクチャを採用する:

| コンポーネント             | 役割                                                                  |
| ------------------- | ------------------------------------------------------------------- |
| **Driver Program**  | アプリケーションのメインプロセス。SparkContext を生成し、ユーザーコードを DAG（有向非巡回グラフ）に変換して実行を管理 |
| **Cluster Manager** | クラスタ全体のリソース割り当てを管理。YARN、Kubernetes、Mesos、スタンドアロンモードに対応              |
| **Executor**        | ワーカーノード上で実行される分散エージェント。タスクの実行とデータのキャッシュを担当                          |
| **SparkContext**    | Spark 実行環境への接続を確立するメインエントリポイント                                      |

#### 実行フロー

1. ユーザーコードが Driver に送信される
2. Driver が処理を DAG に変換
3. DAG Scheduler が DAG をステージに分割
4. Task Scheduler がタスクを Executor に配分
5. Executor がタスクを並列実行し、結果を返却

### コアデータ構造

| データ構造                                  | 特徴                                             |
| -------------------------------------- | ---------------------------------------------- |
| **RDD（Resilient Distributed Dataset）** | 不変の分散データセット。耐障害性を持ち、変換操作のリネージ（系譜）を記録。Spark の基盤 |
| **DataFrame**                          | RDD の上に構築された名前付きカラムを持つ分散テーブル。[[SQL]] ライクな操作が可能 |
| **Dataset**                            | DataFrame に型安全性を加えた API（JVM 言語のみ）              |

### 主要コンポーネント

| コンポーネント | 役割 |
|--------------|------|
| **Spark SQL** | 構造化データに対する SQL クエリと DataFrame API を提供。[[Apache Hive]] の HiveQL とも互換 |
| **Structured Streaming** | DataFrame API と同じインターフェースでストリーム処理を実現。マイクロバッチおよびリアルタイムモードに対応 |
| **MLlib** | 分散機械学習ライブラリ。分類、回帰、クラスタリング、協調フィルタリング等を提供 |
| **GraphX** | グラフ並列計算ライブラリ |
| **PySpark** | Python から Spark を利用するためのインターフェース |
| **Spark Connect** | クライアント-サーバーアーキテクチャでリモートクラスタへの接続を実現 |

### Spark 4.x の新機能

#### Spark 4.0（2025年5月）

- **Spark Connect**: クライアント-サーバーアーキテクチャにより、Spark をサービスとして利用可能に
- **ANSI モードデフォルト化**: SQL 標準準拠をデフォルトに変更
- **SQL スクリプティング**: セッション変数、制御フロー（IF/WHILE）のサポート
- **PIPE 構文**: Unix パイプライクな直感的な SQL 構文
- **再利用可能な SQL UDF**: SQL で関数を定義・再利用

#### Spark 4.1（2025年12月）

- **Spark Declarative Pipelines（SDP）**: データセットとクエリを宣言的に定義し、実行グラフ・依存順序・並列化・チェックポイント・リトライを Spark が自動管理するフレームワーク
- **Structured Streaming Real-Time Mode（RTM）**: サブ秒レイテンシの連続処理。ステートレスタスクでは1桁ミリ秒のレイテンシを実現
- **SQL スクリプティング GA**: エラーハンドリング改善とともに正式リリース
- **VARIANT 型 GA**: 半構造化データの効率的な処理。シュレッディングによる高速読み取り

### 主な活用領域

| 領域 | 説明 |
|------|------|
| **ETL / データパイプライン** | 大規模データの抽出・変換・ロード処理 |
| **リアルタイムストリーミング** | IoT データ、ログ分析、イベント駆動処理 |
| **機械学習** | MLlib や外部ライブラリを用いた分散 ML モデル構築 |
| **データレイクハウス** | [[Apache Iceberg]] や Delta Lake と組み合わせたモダンデータアーキテクチャ |
| **対話的 SQL 分析** | Spark SQL による大規模データの ad-hoc クエリ |

### Spark と Hadoop MapReduce の比較

| 観点 | Apache Spark | MapReduce |
|------|-------------|-----------|
| 処理方式 | インメモリ | ディスクベース |
| 速度 | 最大100倍高速 | 安定だが低速 |
| リアルタイム処理 | Structured Streaming で対応 | バッチのみ |
| 機械学習 | MLlib 内蔵 | 外部ツールが必要 |
| 対話的クエリ | Spark SQL で対応 | 不向き |
| 実行環境 | YARN / K8s / スタンドアロン | YARN |

## ポイント

- UC Berkeley AMPLab 発の分散処理エンジンで、インメモリ処理により MapReduce を大幅に高速化
- バッチ処理・ストリーミング・ML・SQL 分析を単一フレームワークで統一的に扱える
- Spark 4.x では Spark Connect によるサービス化、Declarative Pipelines による宣言的パイプライン、Real-Time Mode によるサブ秒ストリーミングが導入
- [[データレイクハウス]]の標準的な計算エンジンとして、[[Apache Iceberg]] や Delta Lake と密接に連携
- PySpark により Python エコシステムとの統合が容易で、データサイエンス・ML ワークフローに広く採用

## 関連項目

- [[Apache Hadoop]] - Spark の基盤となる分散処理エコシステム
- [[Apache Hive]] - Spark SQL が HiveQL と互換性を持つ SQL 分析基盤
- [[データレイク]] - Spark が処理エンジンを担うデータ蓄積アーキテクチャ
- [[データレイクハウス]] - Spark を中核エンジンとするモダンデータアーキテクチャ
- [[データウェアハウス]] - Spark SQL が代替・補完する分析基盤
- [[データ基盤]] - Spark が構成要素となるデータ基盤全体像
- [[Apache Iceberg]] - Spark と密接に統合するオープンテーブルフォーマット
- [[Apache Parquet]] - Spark が標準的に扱う列指向ファイルフォーマット
- [[MLOps]] - Spark MLlib を活用した ML ライフサイクル管理
- [[SQL]] - Spark SQL の基盤となる標準クエリ言語

## 参考

- [Apache Spark 公式サイト](https://spark.apache.org/)
- [Apache Spark History](https://spark.apache.org/history.html)
- [Spark Release 4.1.0](https://spark.apache.org/releases/spark-release-4.1.0.html)
- [Spark Release 4.0.0](https://spark.apache.org/releases/spark-release-4-0-0.html)
- [Databricks - Introducing Apache Spark 4.0](https://www.databricks.com/blog/introducing-apache-spark-40)
- [Databricks - Introducing Apache Spark 4.1](https://www.databricks.com/blog/introducing-apache-sparkr-41)
- [Apache Spark - Wikipedia](https://en.wikipedia.org/wiki/Apache_Spark)
