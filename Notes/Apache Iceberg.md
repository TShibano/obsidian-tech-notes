---
title: "Apache Iceberg"
date: 2026-02-07
tags:
  - Cloud
  - DB
related:
  - "[[データレイク]]"
  - "[[データ基盤]]"
  - "[[データウェアハウス]]"
  - "[[Apache Parquet]]"
  - "[[Apache Avro]]"
  - "[[オブジェクトストレージ]]"
  - "[[データカタログ]]"
  - "[[データレイクハウス]]"
  - "[[data version control]]"
---

## 概要

Apache Iceberg は、大規模な分析データセット向けに設計されたオープンソースの**テーブルフォーマット**である。2017年に Netflix と Apple のエンジニアによって作成され、2020年に Apache トップレベルプロジェクトとなった。[[データレイク]]上のファイル群を「テーブル」として管理し、ACID トランザクション、タイムトラベル、スキーマ進化などの機能を提供することで、データレイクにデータウェアハウス相当の信頼性をもたらす。

## 詳細

### 必要な層（アーキテクチャ）

Apache Iceberg のアーキテクチャは3つの層で構成される:

```
┌─────────────────────────────┐
│      カタログ層（Catalog）      │  テーブル名 → 現在のメタデータへのポインタ
├─────────────────────────────┤
│    メタデータ層（Metadata）     │  スナップショット、マニフェスト、統計情報
├─────────────────────────────┤
│      データ層（Data）          │  Parquet / ORC / Avro ファイル
└─────────────────────────────┘
```

#### 1. カタログ層（Catalog Layer）

テーブルの現在のメタデータへのポインタを管理する最上位の層:

| 役割 | 説明 |
|------|------|
| **テーブル管理** | テーブルの作成・削除・名前変更などの操作を処理 |
| **メタデータポインタ** | テーブル名と現在のメタデータファイルの場所をマッピング |
| **アトミックなコミット** | メタデータポインタの更新により、テーブル変更のアトミック性を保証 |

対応カタログ実装: Hive Metastore、AWS Glue、Nessie、Apache Polaris、JDBC カタログなど

#### 2. メタデータ層（Metadata Layer）

テーブルの状態を管理する中間層。以下の3種類のファイルで構成される:

| ファイル | 説明 |
|---------|------|
| **メタデータファイル** | テーブルのスキーマ、パーティション仕様、スナップショットの履歴を JSON で記録 |
| **マニフェストリスト** | スナップショットに対応するマニフェストファイルの一覧。各マニフェストの統計サマリーを含む |
| **マニフェストファイル** | 個々のデータファイルのメタデータ（ファイルパス、パーティション値、列レベルの統計情報）を [[Apache Avro]] 形式で記録 |

テーブルに変更が加えられるたびに新しいスナップショットが作成される。スナップショットは不変（イミュータブル）であり、テーブル状態の履歴を提供する。

#### 3. データ層（Data Layer）

実際のデータファイルが格納される層。[[オブジェクトストレージ]]（S3、GCS、ADLS 等）上に [[Apache Parquet]]、ORC、[[Apache Avro]] 形式で保存される。Iceberg 自体はデータの格納方式に依存せず、メタデータ層を通じてデータファイルを管理する。

### メリット

#### ACID トランザクション

楽観的同時実行制御により、複数のプロセスが同時にテーブルを読み書きしても整合性が保たれる。書き込みは新しいスナップショットを作成するが、並行クエリには影響しない（スナップショット分離）。

#### タイムトラベル

過去のスナップショットを参照して、任意の時点のテーブル状態にアクセスできる。誤った操作があった場合に特定時点までデータを巻き戻すことが可能。監査やデバッグに活用される。

#### スキーマ進化

ダウンタイムなしでテーブルスキーマを変更可能:

- 列の追加・削除・名前変更・型の拡張・順序変更
- スキーマの更新はメタデータの変更のみで、データファイルの書き換えが不要
- 列は ID で追跡されるため、名前変更時にもデータの整合性を維持

#### 隠れたパーティショニング（Hidden Partitioning）

ユーザーがパーティション列を意識せずにクエリを記述できる。Iceberg がメタデータからパーティション情報を自動的に推論し、不要なデータファイルをスキップする。パーティション仕様の変更もメタデータのみの操作で、データの再書き込みが不要。

#### ベンダー中立・エンジン非依存

Spark、Trino、Flink、Presto、Hive、Impala、Athena、BigQuery、Snowflake など多数のクエリエンジンからアクセス可能。特定のエンジンやベンダーにロックインされない。

### 最新動向（2025〜2026年）

#### Delta Lake との融合

Databricks が Iceberg の共同創設者が設立した Tabular 社を買収。Delta Lake と Iceberg のメタデータ共有による直接的なテーブル共有を目指している。両フォーマットのクライアントが同じメタデータを利用できる統合が計画されている。

#### Apache Polaris（インキュベーション）

Apache Foundation にインキュベーションとして寄贈された Iceberg 用の REST カタログ。マルチエンジン・マルチクラウド環境での標準的なカタログ実装を目指す。

#### クラウドベンダーのネイティブ対応

- **AWS**: S3 Tables で Iceberg をネイティブサポート
- **Google Cloud**: BigLake Iceberg ネイティブストレージ
- **Snowflake**: Iceberg Tables のネイティブサポート
- **Databricks**: フル Iceberg サポートを発表

#### ストリーミング統合の進化

2026年は「ストリーミングファーストレイクハウス」の年とされ、Apache Flink や Kafka との統合により、インジェスト・処理・クエリが継続的に行われるアーキテクチャが成熟しつつある。

#### Format V3 の検討

列型の改善、クラウドネイティブエンコーディング、ストリーミングサポートの強化を含む V3 フォーマットが2026年に策定される見込み。

## ポイント

- カタログ層・メタデータ層・データ層の3層構造で、[[データレイク]]上のファイルを「テーブル」として管理
- ACID トランザクション、タイムトラベル、スキーマ進化により、データレイクにデータウェアハウスの信頼性をもたらす
- メタデータ層が [[Apache Avro]]、データ層が [[Apache Parquet]] を標準的に採用
- ベンダー中立・エンジン非依存で、Spark・Trino・Flink・Athena・BigQuery・Snowflake 等から利用可能
- Delta Lake との融合が進行中。クラウドベンダー各社がネイティブ対応を強化

## 関連項目

- [[データレイク]]
- [[データ基盤]]
- [[データウェアハウス]]
- [[Apache Parquet]]
- [[Apache Avro]]
- [[オブジェクトストレージ]]
- [[データカタログ]]
- [[データレイクハウス]] - Iceberg が中核を担うデータアーキテクチャ
- [[data version control]] - Iceberg のスナップショットによるデータバージョニング

## 参考

- [Apache Iceberg 公式サイト](https://iceberg.apache.org/)
- [AWS - What is Apache Iceberg?](https://aws.amazon.com/what-is/apache-iceberg/)
- [Google Cloud - What is Apache Iceberg?](https://cloud.google.com/discover/what-is-apache-iceberg)
- [IBM - What Is Apache Iceberg?](https://www.ibm.com/think/topics/apache-iceberg)
- [Databricks - Announcing full Apache Iceberg support](https://www.databricks.com/blog/announcing-full-apache-iceberg-support-databricks)
- [DEV Community - 2025-2026 Guide to Apache Iceberg](https://dev.to/alexmercedcoder/2025-2026-guide-to-learning-about-apache-iceberg-data-lakehouse-agentic-ai-2a0b)
