---
title: "Apache Hive"
date: 2026-02-10
tags:
  - Cloud
  - DB
related:
  - "[[Apache Hadoop]]"
  - "[[データレイク]]"
  - "[[データレイクハウス]]"
  - "[[データウェアハウス]]"
  - "[[データ基盤]]"
  - "[[Apache Iceberg]]"
  - "[[Apache Parquet]]"
  - "[[Apache Avro]]"
  - "[[SQL]]"
  - "[[Apache Spark]]"
---

## 概要

Apache Hive は、[[Apache Hadoop]] 上に構築された分散データウェアハウスシステムである。SQL ライクなクエリ言語（HiveQL）を用いてペタバイト規模のデータを分析でき、SQL の専門知識を持つ人材がビッグデータ処理に参入する障壁を大幅に下げた。特に Hive Metastore（HMS）はデータレイクのメタデータ管理の事実上の標準として、Hive 本体を超えて広く利用されている。

## 詳細

### 歴史

Apache Hive は、Facebook のデータインフラストラクチャチームが2007年に開発を始めたプロジェクトである。

| 年 | 出来事 |
|----|--------|
| 2007年 | Facebook の Jeff Hammerbacher らが開発を開始。急増するデータを Oracle で管理しきれなくなり、Hadoop に移行する際に SQL インターフェースの必要性から誕生 |
| 2008年8月 | Facebook がオープンソースとして公開 |
| 2008年 | Apache Software Foundation に寄贈 |
| 2010年9月 | Apache トップレベルプロジェクトに昇格 |
| 2024年10月 | Hive 4.0.1 リリース |
| 2025年7月 | Hive 4.1.0 リリース |
| 2025年11月 | Hive 4.2.0 リリース（最新安定版） |

共同開発者は Joydeep Sen Sarma と Ashish Thusoo で、Facebook のデータ基盤チームに所属していた。

### アーキテクチャ

Hive のアーキテクチャは以下の主要コンポーネントで構成される:

| コンポーネント | 役割 |
|--------------|------|
| **Hive Metastore（HMS）** | テーブルのスキーマ、データの格納場所、パーティション情報などのメタデータを一元管理するリポジトリ |
| **Driver** | HiveQL 文の受信・セッション管理・実行のライフサイクル管理を行うコントローラ |
| **Compiler** | HiveQL を実行計画（MapReduce / Tez / Spark ジョブ）に変換 |
| **Optimizer** | 実行計画を最適化（述語プッシュダウン、結合順序最適化など） |
| **Executor** | 最適化された実行計画を Hadoop 上で実行 |

#### 実行エンジン

Hive は複数の実行エンジンをサポートする:

- **MapReduce**: 初期のデフォルトエンジン。バッチ処理向き
- **Apache Tez**: DAG ベースの実行エンジン。MapReduce より高速
- **Apache Spark**: インメモリ処理による高速実行

### HiveQL

HiveQL は SQL に類似したクエリ言語で、以下の特徴を持つ:

- 標準 SQL に近い構文で、SELECT / JOIN / GROUP BY / WINDOW 関数等をサポート
- パーティショニングやバケッティングによるクエリ最適化
- UDF（ユーザー定義関数）による拡張が可能
- TextFile、SequenceFile、ORC、[[Apache Parquet]]、[[Apache Avro]] など多様なデータフォーマットに対応

### Hive Metastore（HMS）

Hive Metastore は Hive の中で最も広く影響を与えたコンポーネントであり、Hive 本体を使わなくても単独で利用されることが多い:

- **メタデータの一元管理**: テーブル定義、カラム情報、パーティション、格納場所などを管理
- **マルチエンジン対応**: Apache Spark、Presto/Trino、Impala など多数のクエリエンジンが HMS をメタデータストアとして利用
- **データレイクの標準**: HDFS やオブジェクトストレージ上のデータレイクにおいて、テーブルメタデータの事実上の標準
- **レイクハウス連携**: [[Apache Iceberg]]、Delta Lake、Apache Hudi などのオープンテーブルフォーマットと組み合わせて利用

#### HMS の後継・代替カタログ

モダンデータスタックでは HMS の制約（リネージ追跡の欠如、マルチワークスペースガバナンスの不足など）を補うため、次世代カタログが登場している:

| カタログ | 特徴 |
|---------|------|
| **Unity Catalog** | Databricks が開発し Linux Foundation に寄贈。テーブル・ML モデル・ボリュームを統合管理 |
| **Apache Polaris** | Snowflake が開発し Apache に寄贈。Iceberg テーブル専用のオープンソースカタログ |
| **Project Nessie** | Git ライクなブランチ管理でデータのバージョン管理を実現 |
| **Gravitino** | マルチカタログフェデレーションを提供 |

ただし、HMS は後方互換性と広範なエコシステムサポートにより、依然として多くの環境で利用されている。

### Hive 4.x の新機能

Hive 4.2.0（2025年11月リリース）の主な新機能:

- **JDK 21 サポート**（最小要件として JDK 21 を要求）
- **Iceberg v3 対応強化**: Deletion Vectors、カラムデフォルト値、Z-ordering、Variant 型
- **自動コンパクション**: Iceberg テーブルの自動コンパクション機能を内蔵
- **REST Catalog クライアント**: Iceberg REST Catalog への接続をサポート
- **HMS REST Catalog**: Hive Metastore 自体を REST Catalog として公開

### Hive と Spark SQL の比較

| 観点 | Apache Hive | Spark SQL |
|------|------------|-----------|
| 処理方式 | バッチ処理が中心 | バッチ + リアルタイム |
| 性能 | 大規模バッチで安定 | インメモリ処理で高速 |
| レイテンシ | 高い（バッチ向け） | 低い（対話的クエリ向け） |
| 主な用途 | ETL、大規模バッチ分析 | 対話的分析、ML パイプライン |
| 実行環境 | Hadoop クラスタ | Hadoop / スタンドアロン / K8s |

現代では Spark SQL が対話的クエリの主流となっているが、Hive は大規模バッチ ETL やレガシーワークロードにおいて引き続き利用されている。

## ポイント

- Facebook が2007年に開発を開始し、SQL の専門家がビッグデータを扱えるようにした先駆的プロジェクト
- Hive Metastore は Hive 本体以上に影響力を持ち、データレイクのメタデータ管理のデファクトスタンダード
- Spark、Presto/Trino、Impala など多数のクエリエンジンが HMS を共通メタデータストアとして利用
- Hive 4.x では [[Apache Iceberg]] との統合が大幅に強化され、モダンレイクハウスへの対応が進行中
- 次世代カタログ（Unity Catalog、Apache Polaris 等）が登場しつつも、HMS は後方互換性により広く使われ続けている

## 関連項目

- [[Apache Hadoop]] - Hive の基盤となる分散処理フレームワーク
- [[データレイク]] - HMS がメタデータ管理を担うデータ蓄積アーキテクチャ
- [[データレイクハウス]] - Hive の進化形としてのモダンアーキテクチャ
- [[データウェアハウス]] - Hive が提供する SQL 分析基盤の役割
- [[データ基盤]] - Hive が構成要素となるデータ基盤全体像
- [[Apache Iceberg]] - Hive 4.x でネイティブサポートされるオープンテーブルフォーマット
- [[Apache Parquet]] - Hive が対応する列指向ファイルフォーマット
- [[Apache Avro]] - Hive が対応する行指向シリアライゼーション
- [[SQL]] - HiveQL の基盤となる標準クエリ言語
- [[Apache Spark]] - Hive の実行エンジンとしても利用される分散処理エンジン

## 参考

- [Apache Hive 公式サイト](https://hive.apache.org/)
- [Apache Hive ドキュメント](https://hive.apache.org/docs/latest/)
- [Apache Hive - Wikipedia](https://en.wikipedia.org/wiki/Apache_Hive)
- [Apache Software Foundation Announces Apache Hive 4.0](https://news.apache.org/foundation/entry/apache-software-foundation-announces-apache-hive-4-0)
- [lakeFS - Hive Metastore: Why It's Still Here and What Can Replace It](https://lakefs.io/blog/hive-metastore-why-its-still-here-and-what-can-replace-it/)
- [Starburst - Apache Hive: Understanding the Definition & Use Cases](https://www.starburst.io/blog/what-is-apache-hive/)
