---
title: "ETL"
date: 2026-05-03
tags:
  - DevOps
  - DB
related:
  - "[[データ基盤]]"
  - "[[データウェアハウス]]"
  - "[[Apache Airflow]]"
  - "[[dbt]]"
  - "[[ELT]]"
  - "[[リバースETL]]"
  - "[[バッチ処理]]"
  - "[[リアルタイム処理]]"
---

## 概要

ETL（Extract, Transform, Load）はデータ統合の伝統的なパターンで，ソースシステムからデータを**抽出（Extract）**し，**変換（Transform）**してから，ターゲット（DWH・データマート等）に**ロード（Load）**する．変換処理をロード前に行う点が特徴で，オンプレミス時代の制約された DWH ストレージとコンピュートを前提として発展した．クラウド時代には変換を後工程に移した [[ELT]] への移行が進んでいる．

## 詳細

### 3フェーズの処理

```
[ソースシステム群]
  DB / API / ファイル / SaaS
       ↓ Extract（抽出）
[ステージング領域]
  生データを一時格納
       ↓ Transform（変換）
  ・型変換・正規化
  ・クレンジング・デデュープ
  ・集計・ビジネスルール適用
       ↓ Load（ロード）
[ターゲット]
  データウェアハウス / データマート
```

#### 抽出（Extract）

- ソースシステム（RDB・API・ファイル・SaaS 等）からデータを取得
- **フル抽出**: 全レコードを毎回取得
- **差分抽出（CDC: Change Data Capture）**: 前回抽出以降の変更分のみを取得．負荷を大幅に削減

#### 変換（Transform）

変換はロード前のステージング領域で実施．CPU/メモリ集約的な処理は ETL ツール側が担う．

| 変換の種類 | 例 |
|-----------|-----|
| クレンジング | NULL 補完，文字列トリム，エンコード修正 |
| 型変換 | 文字列→日付型，通貨の単位統一 |
| 重複排除 | 同一エンティティの統合 |
| 集計 | 日次売上のサマリー計算 |
| ルック アップ結合 | 顧客コードから顧客名を補完 |
| ビジネスルール | 条件分岐，計算式の適用 |

#### ロード（Load）

- **フルロード**: ターゲットを毎回全置換（小テーブル向け）
- **増分ロード（Incremental）**: 差分のみを UPSERT（大テーブル向け）

### ETL の構成要素

```
ETL ツール（オーケストレーション）
├─ コネクタ（ソース・ターゲット接続）
├─ 変換エンジン（処理）
├─ スケジューラ（バッチ実行管理）
├─ エラーハンドリング・リトライ
└─ ログ・監視
```

### 主要 ETL ツール（2026）

| ツール | カテゴリ | 特徴 |
|--------|---------|------|
| **Fivetran** | クラウド EL | コネクタ多数・自動スキーマ変更対応．ローコード |
| **Airbyte** | OSS EL | オープンソース．セルフホスト・クラウド両対応 |
| **[[Apache Airflow]]** | オーケストレーション | Python DAG でパイプライン定義．最も普及 |
| **Dagster** | オーケストレーション | アセット指向．データリネージが強力 |
| **Informatica PowerCenter** | エンタープライズ ETL | 大規模エンタープライズ向け |
| **Talend** | エンタープライズ ETL | オープンソース版あり |
| **AWS Glue** | クラウド ETL | フルマネージド．Spark ベース |
| **Azure Data Factory** | クラウド ETL | Azure エコシステム統合 |
| **[[dbt]]** | 変換（T） | ELT の T 部分特化．SQL ベース |

### ETL vs ELT

| 観点 | ETL | [[ELT]] |
|------|-----|-----|
| 変換タイミング | ロード前（外部） | ロード後（DWH 内部） |
| 変換実行場所 | ETL ツールのサーバー | DWH のコンピュート |
| 生データ保持 | しない（変換済みのみ） | する（再変換が容易） |
| スケール | ツールのスペック依存 | DWH がスケール |
| 適するターゲット | オンプレミス・制限のある DWH | クラウド DWH |
| 柔軟性 | 変換ロジック変更にコスト | 再変換が容易 |

### 2026 年のトレンド

- **Fivetran + dbt Cloud** が最も普及した本番スタック（EL 部分を Fivetran，T 部分を dbt が担う）
- **Apache Airflow** がオーケストレーション層で事実上の標準
- ETL から ELT への移行が加速しているが，レガシー ETL パイプラインの維持も多い
- ストリーミング ETL（バッチではなくリアルタイム処理）需要が増加（Apache Kafka + Flink 等）

## ポイント

- Extract → Transform → Load の順で処理し，変換済みのクリーンなデータを DWH にロードする
- 変換処理を ETL ツール側で行うため，DWH への負荷を抑えられるが，変換エンジンのスペックが処理速度のボトルネック
- CDC（差分抽出）により増分だけを処理することでソースシステムへの負荷と処理時間を削減
- クラウド DWH の普及により変換後ロードの [[ELT]] が主流化しているが，ETL も依然として多用される
- 2026 年は Fivetran + dbt Cloud（ELT 寄り）と Apache Airflow オーケストレーションの組み合わせが主流

## 関連項目

- [[データ基盤]] — ETL はデータ基盤のデータ統合パイプラインの核
- [[データウェアハウス]] — ETL の主なターゲット
- [[Apache Airflow]] — ETL パイプラインのオーケストレーションツール
- [[dbt]] — ELT の変換（T）に特化したツール（ETL の T 部分の現代的代替）
- [[ELT]] — 変換をロード後に行う現代的な代替パターン
- [[リバースETL]] — DWH からビジネスツールへとデータを配信する逆方向パターン
- [[バッチ処理]] — ETL の代表的な実行方式
- [[リアルタイム処理]] — ストリーミング ETL として ETL のリアルタイム版

## 参考

- [Extract, transform, load - Wikipedia](https://en.wikipedia.org/wiki/Extract,_transform,_load)
- [What is ETL? - AWS](https://aws.amazon.com/what-is/etl/)
- [Extract, transform, load (ETL) - Azure Architecture Center](https://learn.microsoft.com/en-us/azure/architecture/data-guide/relational-data/etl)
- [Extract Transform Load (ETL) - Databricks](https://www.databricks.com/discover/etl)
