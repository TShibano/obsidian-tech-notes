---
title: "Apache Hadoop"
date: 2026-02-09
tags:
  - Cloud
  - DB
related:
  - "[[データレイク]]"
  - "[[データレイクハウス]]"
  - "[[Apache Parquet]]"
  - "[[Apache Avro]]"
  - "[[Apache Iceberg]]"
  - "[[Apache Airflow]]"
  - "[[データ基盤]]"
  - "[[データフォーマット]]"
  - "[[オブジェクトストレージ]]"
  - "[[データウェアハウス]]"
  - "[[Apache Hive]]"
  - "[[Apache Spark]]"
---

## 概要

Apache Hadoop は、大規模データセットを分散処理するためのオープンソースフレームワークである。HDFS（分散ファイルシステム）、MapReduce（分散処理モデル）、YARN（リソース管理）の3つのコアコンポーネントで構成され、コモディティハードウェアのクラスタ上で信頼性の高いスケーラブルな分散コンピューティングを実現する。

## 詳細

### 歴史

Apache Hadoop は、Google が2003〜2004年に発表した2つの論文（Google File System、MapReduce）に触発されて開発された。

| 年 | 出来事 |
|----|--------|
| 2003-2004 | Google が GFS 論文と MapReduce 論文を発表 |
| 2006年1月 | Apache Nutch プロジェクトから Hadoop サブプロジェクトとして独立 |
| 2006年4月 | Hadoop 0.1.0 リリース |
| 2011年12月 | Hadoop 1.0.0 リリース |
| 2013年10月 | Hadoop 2.0.0 リリース（YARN 導入） |
| 2017年12月 | Hadoop 3.0.0 リリース |
| 2025年8月 | Hadoop 3.4.2 リリース（最新安定版） |

名前の由来は、開発者 Doug Cutting（当時 Yahoo! 勤務）の息子が持っていたおもちゃの象にちなんで付けられた。

### コアコンポーネント

#### HDFS（Hadoop Distributed File System）

Java で書かれた分散ファイルシステムで、大規模データを複数ノードにまたがって格納する。

- **NameNode**: ファイルシステムのメタデータ（ファイル名、ブロック情報、配置場所、権限）を管理するマスターノード
- **DataNode**: 実際のデータブロックを格納するワーカーノード
- **ブロック分割**: ファイルを大きなブロック（デフォルト128MB）に分割し、クラスタ全体に分散配置
- **レプリケーション**: デフォルトで3つのレプリカを異なるノードに保持し、耐障害性を確保

#### MapReduce

大規模データの分散並列処理を行うプログラミングモデル。

- **Map フェーズ**: 入力データを分割し、各ノードで並列に中間キー・バリューペアを生成
- **Shuffle フェーズ**: 同一キーのデータを集約して同じノードに転送
- **Reduce フェーズ**: 集約されたデータに対して最終的な処理を実行
- **データローカリティ**: 処理コードをデータが存在するノードに転送することで、ネットワーク転送を最小化

#### YARN（Yet Another Resource Negotiator）

Hadoop 2.0 で導入されたリソース管理フレームワーク。MapReduce 1.0 の制約を克服するため、リソース管理とジョブスケジューリングを分離した。

- **ResourceManager（RM）**: クラスタ全体のリソースを管理するグローバルデーモン
- **ApplicationMaster（AM）**: アプリケーションごとのジョブスケジューリングとモニタリングを担当
- **NodeManager**: 各ノードのリソースを管理し、コンテナを実行

YARN の導入により、MapReduce 以外のフレームワーク（Apache Spark 等）も Hadoop クラスタ上で実行可能になった。

### エコシステム

Hadoop を中心に広大なエコシステムが発展した:

| コンポーネント | 役割 |
|--------------|------|
| **Apache Hive** | SQL ライクなクエリ言語（HiveQL）で HDFS 上のデータを分析。Hive Metastore はテーブル定義のカタログとして広く利用 |
| **Apache HBase** | HDFS 上で動作する分散カラム型データベース。BigTable ライクなリアルタイム読み書きを提供 |
| **Apache Spark** | インメモリ分散処理エンジン。MapReduce より大幅に高速（最大100倍）。ML・ストリーミングにも対応 |
| **Apache Pig** | データフロー言語による ETL 処理 |
| **Apache ZooKeeper** | 分散システムのコーディネーションサービス |
| **[[Apache Airflow]]** | ワークフロー管理・スケジューリング |

### クラウド時代における Hadoop の位置づけ

Hadoop は「衰退した」と語られることもあるが、実態はより複雑である:

- **市場規模**: 2024年に約65億ドル、2025年には80億ドル超に成長見込み。世界で約22万企業が利用
- **進化**: Hadoop 3.4.2 ではクラウドストレージコネクタの性能向上やセキュリティ強化が行われ、クラウドネイティブ環境への適応が進んでいる
- **コンポーネントの分離**: HDFS → [[オブジェクトストレージ]]（S3 等）、MapReduce → Spark、Hive Metastore → 独立したカタログサービスへの移行が進み、Hadoop エコシステムの各コンポーネントが独立して進化
- **レイクハウスへの発展**: Hadoop の[[データレイク]]アーキテクチャは、[[Apache Iceberg]] や [[Apache Parquet]] を活用した[[データレイクハウス]]へと発展している

### Hadoop と主要技術の関係

| 技術 | Hadoop との関係 |
|------|----------------|
| [[Apache Parquet]] | Hadoop エコシステムで標準的な列指向ファイルフォーマット |
| [[Apache Avro]] | Hadoop エコシステムで広く使われる行指向シリアライゼーション |
| [[Apache Iceberg]] | Hadoop 上のテーブルフォーマットとして発展、現在はクラウドネイティブにも対応 |
| [[オブジェクトストレージ]] | HDFS の代替として S3 等がデータレイクのストレージ層に |
| [[DuckDB]] | Parquet ファイルを直接クエリでき、Hadoop なしの軽量分析を実現 |

## ポイント

- Hadoop は Google の GFS・MapReduce 論文に触発されて2006年に誕生した分散処理フレームワーク
- HDFS（ストレージ）、MapReduce（処理）、YARN（リソース管理）の3層構成
- YARN の導入により MapReduce 以外のフレームワーク（Spark 等）もクラスタ上で実行可能に
- 広大なエコシステム（Hive、HBase、Spark、ZooKeeper 等）を形成
- クラウド時代では各コンポーネントが分離・独立して進化し、[[データレイクハウス]]アーキテクチャへ発展
- 依然として世界22万企業が利用しており、Hadoop 3.x 系は継続的に開発・リリースされている

## 関連項目

- [[データレイク]] - Hadoop が先駆けとなった大規模データ蓄積アーキテクチャ
- [[データレイクハウス]] - Hadoop のデータレイクから発展した次世代アーキテクチャ
- [[データウェアハウス]] - Hive が担っていた SQL 分析基盤の役割
- [[Apache Parquet]] - Hadoop エコシステムの標準列指向フォーマット
- [[Apache Avro]] - Hadoop エコシステムの標準行指向シリアライゼーション
- [[Apache Iceberg]] - Hadoop 上のテーブルフォーマットから発展
- [[Apache Airflow]] - Hadoop ジョブを含むワークフローのスケジューリング
- [[オブジェクトストレージ]] - HDFS の代替として普及したクラウドストレージ
- [[データ基盤]] - Hadoop が構成要素となるデータ基盤全体像
- [[データフォーマット]] - Hadoop エコシステムで扱われるデータ形式の体系
- [[Apache Hive]] - Hadoop エコシステムの SQL 分析基盤
- [[Apache Spark]] - Hadoop エコシステムのインメモリ分散処理エンジン

## 参考

- [Apache Hadoop 公式サイト](https://hadoop.apache.org/)
- [Apache Hadoop 3.4.2 ドキュメント](https://hadoop.apache.org/docs/current/)
- [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)
- [Apache Hadoop リリースノート](https://hadoop.apache.org/releases.html)
- [Databricks - Hadoop Ecosystem](https://www.databricks.com/glossary/hadoop-ecosystem)
