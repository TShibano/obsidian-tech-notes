---
title: "OLAP"
date: 2026-04-18
tags:
  - DB
related:
  - "[[OLTP]]"
  - "[[データウェアハウス]]"
  - "[[DuckDB]]"
  - "[[SQL]]"
  - "[[NoSQL]]"
---

## 概要

OLAP（Online Analytical Processing）は、複雑な分析クエリに対して高速に回答するデータ処理方式である。日本語では「オンライン分析処理」または「多次元分析」とも呼ばれ、BI（Business Intelligence）の中核を担う。データウェアハウス（[[データウェアハウス]]）と組み合わせて使われることが多く、KPI 集計・トレンド分析・意思決定支援に利用される。

## 詳細

### [[OLTP]] との比較

| 観点 | OLAP | [[OLTP]] |
|------|------|------|
| 目的 | 分析・集計 | 業務処理・トランザクション |
| クエリ | 複雑な集計、大量レコード | シンプル、少数レコード |
| 操作 | 読み取り中心 | 書き込み中心 |
| データ量 | 数億〜数兆行規模 | 比較的小〜中規模 |
| レスポンス | 秒〜分単位 | ミリ秒単位 |
| DB 構造 | 列指向（カラムナー） | 行指向（ロー指向） |
| スキーマ | スタースキーマ・スノーフレーク | 正規化された RDBMS |
| 更新頻度 | バッチ更新（日次など） | リアルタイム更新 |

### 列指向（カラムナー）ストレージ

OLAP の性能の鍵は**列指向ストレージ**にある。行指向と列指向の違い:

```
行指向（OLTP向き）:
[id=1, name=Alice, age=30] [id=2, name=Bob, age=25] ...

列指向（OLAP向き）:
[id: 1,2,3,...] [name: Alice,Bob,...] [age: 30,25,...]
```

列指向のメリット:
- **圧縮効率が高い**: 同じ型のデータが連続するため圧縮率が向上
- **集計クエリが高速**: `SUM(age)` のような操作で必要な列だけ読み込む
- **I/O 削減**: 不要な列を読み飛ばせる

### OLAP の主要操作

| 操作 | 説明 | 例 |
|------|------|-----|
| **ロールアップ** | 詳細→集約（drill up） | 日別 → 月別 → 年別 に集計 |
| **ドリルダウン** | 集約→詳細（drill down） | 年別 → 月別 → 日別 に掘り下げ |
| **スライス** | 1次元で切り出し | 特定年度のデータを抽出 |
| **ダイス** | 複数次元で切り出し | 東京 × Q1 × 商品カテゴリA のデータ |
| **ピボット** | 次元の入れ替え | 行・列を入れ替えて別の視点で分析 |

### OLAP の種類

| 種類 | 概要 |
|------|------|
| **MOLAP**（多次元 OLAP） | データを多次元キューブとして事前集計して高速化 |
| **ROLAP**（リレーショナル OLAP） | RDBMS 上で SQL を使って OLAP を実現 |
| **HOLAP**（ハイブリッド OLAP） | MOLAP と ROLAP を組み合わせ |

### 代表的な OLAP データベース・ツール

| 製品 | 種別 | 特徴 |
|------|------|------|
| **[[DuckDB]]** | OSS 埋め込み型 | インプロセス実行。分析 SQL に特化した超高速処理 |
| **ClickHouse** | OSS / クラウド | 超高速列指向 DB。毎秒数十億行の集計が可能 |
| **Apache Druid** | OSS | リアルタイム OLAP。イベントデータの即時集計 |
| **BigQuery** | Google Cloud | サーバーレス。ペタバイト規模の分析 |
| **Redshift** | AWS | 列指向 DWH。PostgreSQL 互換 |
| **Snowflake** | クラウド | ストレージとコンピュートを分離したクラウド DWH |

### BI ツールとの連携

OLAP データベースは BI ツールと組み合わせて使う:

- **Tableau**, **Power BI**, **Looker**, **Grafana** → 可視化・ダッシュボード
- **dbt** → データ変換・集計レイヤーの管理

## ポイント

- OLAP の性能は列指向ストレージによって実現される。分析クエリでは必要な列だけ読み込めるため I/O が大幅に削減される
- [[OLTP]]（業務処理）と OLAP（分析）では目的・最適化の方向が真逆のため、通常は別のシステムで運用する
- データフロー: OLTP DB → ETL/ELT → [[データウェアハウス]] → OLAP → BI ツール
- [[DuckDB]] は OLAP に特化した組み込み型 DB として近年急速に普及
- ClickHouse は専用 OLAP DB として超高速な集計性能を誇る

## 関連項目

- [[OLTP]] - トランザクション処理との対比
- [[データウェアハウス]] - OLAP データの格納先
- [[DuckDB]] - 代表的な OSS 組み込み型 OLAP エンジン
- [[SQL]] - OLAP クエリの記述に使用
- [[NoSQL]] - 一部の NoSQL DB（ワイドカラム型など）は OLAP 的な用途にも使われる

## 参考

- [What is OLAP? - AWS](https://aws.amazon.com/what-is/olap/)
- [What is OLAP? | Databricks](https://www.databricks.com/blog/what-is-olap)
- [What is OLAP? | ClickHouse](https://clickhouse.com/resources/engineering/what-is-olap)
- [OLTP vs OLAP - Difference Between Data Processing Systems - AWS](https://aws.amazon.com/compare/the-difference-between-olap-and-oltp/)
- [OLAP - Wikipedia（日本語）](https://ja.wikipedia.org/wiki/OLAP)
