---
title: "NoSQL"
date: 2026-02-18
tags:
  - DB
  - NoSQL
related:
  - "[[SQL]]"
  - "[[NewSQL]]"
  - "[[PostgreSQL]]"
  - "[[データウェアハウス]]"
  - "[[DuckDB]]"
---

## 概要

NoSQL（Not Only SQL）は、リレーショナルデータベース（RDBMS）以外のデータモデルを採用した非リレーショナルデータベースの総称である。固定スキーマを持たず水平スケールを得意とし、大規模データや多様なデータ構造を扱うユースケースで [[SQL]] ベースの RDBMS を補完・代替する存在として普及した。

## 詳細

### 背景と歴史

2000年代後半、Google・Amazon・Facebook などの大規模 Web サービスが RDBMS の垂直スケールの限界に直面したことを契機に急速に発展した。

- **2006年**: Google が Bigtable（ワイドカラム型）の論文を発表
- **2007年**: Amazon が Dynamo（キーバリュー型）の論文を発表
- **2009年**: MongoDB・Cassandra などが OSS として登場。「NoSQL」という用語が広まる
- **2010年代**: DynamoDB・Redis・Neo4j などが多様な用途で定着
- **2020年代**: [[NewSQL]] との境界が曖昧化しつつ、クラウドマネージドサービスとして成熟

### NoSQL の主要タイプ

#### 1. キーバリュー型（Key-Value Store）

最もシンプルなモデル。キーと値のペアで保存し、キーによる検索が O(1) で高速。

| 代表 DB | 特徴 |
|---------|------|
| Redis | インメモリ型。毎秒数百万リクエストを処理可能。セッション管理・キャッシュ・Pub/Sub |
| DynamoDB | AWS マネージド。キーバリューとドキュメントのハイブリッド。サーバーレス対応 |
| etcd | 分散設定管理。Kubernetes のデータストアとして採用 |

#### 2. ドキュメント型（Document Store）

JSON/BSON/XML 形式のドキュメントを保存。ネストされた構造や可変スキーマに対応。

| 代表 DB | 特徴 |
|---------|------|
| MongoDB | 最も広く使われるドキュメント DB。柔軟なスキーマと豊富なクエリ機能 |
| Couchbase | 分散型。モバイル同期機能あり |
| Firestore | Google Cloud のサーバーレスドキュメント DB |

#### 3. ワイドカラム型（Wide-Column Store）

行と列の概念を持つが、列が行ごとに異なる（スパースな列管理）。書き込みスループットに強い。

| 代表 DB | 特徴 |
|---------|------|
| Cassandra | 分散型。毎秒数百万の書き込みに耐えるハイスループット。時系列・IoT データ向き |
| HBase | Hadoop/HDFS 上で動作。Google Bigtable の OSS 実装 |

#### 4. グラフ型（Graph Database）

ノード（エンティティ）とエッジ（関係）でデータを表現。複雑な関係のクエリに特化。

| 代表 DB | 特徴 |
|---------|------|
| Neo4j | 最もメジャーなグラフ DB。Cypher クエリ言語を採用 |
| Amazon Neptune | AWS マネージドグラフ DB。RDF/SPARQL にも対応 |

#### その他のカテゴリ

| タイプ | 代表 DB | 主なユースケース |
|--------|---------|--------------|
| 時系列型 | InfluxDB, Amazon Timestream | IoT センサー、監視メトリクス |
| 検索エンジン | Elasticsearch, OpenSearch | 全文検索、ログ分析 |
| インメモリ型 | Redis, Memcached | 高速キャッシュ、リアルタイム処理 |

### SQL vs NoSQL の使い分け

| 観点 | SQL（RDBMS） | NoSQL |
|------|------------|-------|
| スキーマ | 固定（厳格） | 柔軟（動的） |
| スケール | 垂直スケール中心 | 水平スケール容易 |
| トランザクション | ACID 保証 | 結果整合性（BASE）が多い |
| 得意なデータ | 構造化データ | 半構造化・非構造化データ |
| クエリ | 複雑な JOIN、集計 | 特定アクセスパターンへの最適化 |
| 向いているケース | 金融取引、在庫管理 | ソーシャル、IoT、キャッシュ、ログ |

### BASE 原則

NoSQL の多くは ACID の代わりに **BASE**（Basically Available, Soft State, Eventual Consistency）を採用する:

- **Basically Available**: 一部ノードが障害を起こしても応答を返す
- **Soft State**: データは時間とともに変化する可能性がある
- **Eventual Consistency**: 最終的には一貫した状態に収束する

### ポリグロット・パーシステンス

単一サービス内で複数の NoSQL/SQL データベースを目的別に使い分けるアーキテクチャ:

- キャッシュには Redis
- ユーザープロフィールには MongoDB
- ソーシャルグラフには Neo4j
- 分析には [[DuckDB]] や Redshift

## ポイント

- NoSQL は「SQL の代替」ではなく「目的に応じたデータストアの選択」という考え方が重要
- 4 大カテゴリ（キーバリュー・ドキュメント・ワイドカラム・グラフ）にはそれぞれ固有の強みがある
- スケーラビリティや柔軟なスキーマが必要な場合に有効だが、ACID 保証が必要な用途には適さない
- [[NewSQL]] は NoSQL の水平スケールと SQL の ACID を両立させる第3の選択肢
- クラウドマネージドサービス（DynamoDB・Firestore など）により、運用コストが大幅に下がっている

## 関連項目

- [[SQL]] - リレーショナルデータベースの標準クエリ言語
- [[NewSQL]] - NoSQL の水平スケールと SQL の ACID を両立する新世代 DB
- [[PostgreSQL]] - 拡張性の高い OSS RDBMS。JSON サポートで NoSQL 的な使い方も可能
- [[データウェアハウス]] - 分析用データ基盤。columnar 型 NoSQL と用途が重なる場合も
- [[DuckDB]] - 分析用インプロセス SQL DB。NoSQL の一部用途を代替できる

## 参考

- [What is NoSQL? - AWS](https://aws.amazon.com/nosql/)
- [NoSQL databases: Types, use cases, and 8 databases to try - Instaclustr](https://www.instaclustr.com/education/nosql-database/nosql-databases-types-use-cases-and-8-databases-to-try/)
- [NoSQL Databases Visually Explained - AltexSoft](https://www.altexsoft.com/blog/nosql-databases/)
- [Bigtable: A Distributed Storage System for Structured Data - Google (2006)](https://research.google/pubs/bigtable-a-distributed-storage-system-for-structured-data/)
- [Dynamo: Amazon's Highly Available Key-value Store (2007)](https://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf)
