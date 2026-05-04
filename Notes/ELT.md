---
title: "ELT"
date: 2026-05-03
tags:
  - DevOps
  - DB
related:
  - "[[データ基盤]]"
  - "[[データウェアハウス]]"
  - "[[dbt]]"
  - "[[Snowflake]]"
  - "[[Databricks]]"
  - "[[ETL]]"
  - "[[リバースETL]]"
  - "[[データエンジニア]]"
---

## 概要

ELT（Extract, Load, Transform）はクラウドデータウェアハウス時代に台頭したデータ統合パターン．ソースからデータを**抽出（Extract）**し，生データのまま DWH に**ロード（Load）**し，DWH 内部のコンピュートで**変換（Transform）**する．[[ETL]] が変換をロード前に行うのと対照的で，クラウド DWH の弾力的なコンピュートパワーを活かした設計．[[dbt]] がこのパターンの変換レイヤーを担う代表的ツール．

## 詳細

### ETL との処理フローの違い

```
ETL:  ソース → [Extract] → [Transform] → [Load] → DWH
ELT:  ソース → [Extract] → [Load] → DWH内部で [Transform]
                    生データ                    ↑ dbt/SQL で変換
```

ELT では生データをそのままロードし，変換は DWH のコンピュート（BigQuery・Snowflake・Databricks 等）が担う．

### なぜ ELT が現代の主流なのか

クラウド DWH の3つの特性が ETL の前提を覆した:

| クラウド DWH の特性 | ELT への影響 |
|-------------------|------------|
| **大容量・低コストストレージ** | 生データをそのまま保持してもコスト効率が良い |
| **弾力的コンピュート** | 変換用の大規模並列処理を従量課金で使える |
| **SQL エンジンの高性能化** | 複雑な変換もSQLで高速に実行できる |

### ELT パイプラインの構成

```
データソース（DB / SaaS / API / ファイル）
      ↓ Extract + Load
  Fivetran / Airbyte（EL ツール）
      ↓ 生データをそのままロード
  データウェアハウス（Snowflake / BigQuery / Redshift / Databricks）
      ↓ Transform（DWH 内部の SQL）
  dbt（変換モデルの定義・管理・テスト）
      ↓
  分析レイヤー（BI ツール / データマート）
```

### ELT の利点と注意点

**利点:**
- 生データが DWH に残るため，ビジネスロジック変更時に再変換が容易
- 変換処理が DWH の並列コンピュートにオフロードされるためスケーラブル
- ETL ツールの複雑な変換ロジック管理が不要（SQL に集約）
- 開発者が慣れた SQL/Python で変換を記述できる

**注意点:**
- 変換前の生データが DWH にロードされるため，機密データの扱いに注意
- 変換コストが DWH のコンピュート料金に含まれる（コスト管理が重要）
- クレンジングが不十分な生データがそのまま DWH に蓄積されるリスク

### dbt との組み合わせ

[[dbt]]（data build tool）は ELT の変換（T）を担うツール．SQL モデルをバージョン管理し，テスト・ドキュメント化・リネージ追跡を提供する．

```sql
-- dbt モデルの例（DWH 内部で実行される）
SELECT
    order_id,
    customer_id,
    SUM(amount) AS total_amount,
    COUNT(*) AS item_count
FROM {{ ref('raw_orders') }}
WHERE status = 'completed'
GROUP BY 1, 2
```

### 代表的な ELT スタック（2026）

| 役割 | 代表ツール |
|------|-----------|
| EL（Extract + Load） | Fivetran（クラウド）/ Airbyte（OSS）|
| DWH（変換基盤） | Snowflake / BigQuery / Redshift / Databricks |
| Transform | [[dbt]] |
| オーケストレーション | [[Apache Airflow]] / Dagster |

**Fivetran + dbt Cloud** が 2026 年で最も普及した本番スタック．

### ETL vs ELT の選択基準

| ETL が適する場合 | ELT が適する場合 |
|----------------|----------------|
| オンプレミス・制限 DWH | クラウド DWH（BigQuery/Snowflake 等）|
| 機密データを生データのまま DWH に入れたくない | 生データ保持・再変換の柔軟性が必要 |
| 変換処理が非常に複雑で ETL ツール向き | SQL/Python で変換を記述できる |
| レガシーシステムとの統合 | モダンなデータスタックを構築 |

## ポイント

- 生データを DWH にそのままロードし，変換は DWH 内部の SQL で行う現代的パターン
- クラウド DWH の低コストストレージ・弾力的コンピュートが ELT を実用的にした
- 生データが残るため「変換ロジックの変更 → 再実行」が容易（[[ETL]] の最大の欠点を解消）
- [[dbt]] が変換レイヤーの実質標準ツール．SQL モデル + テスト + リネージを提供
- **Fivetran（EL）+ dbt Cloud（T）+ Snowflake（DWH）** が 2026 年の典型的な本番スタック

## 関連項目

- [[データ基盤]] — ELT はデータ基盤のデータ統合パイプラインの核
- [[データウェアハウス]] — ELT の変換が実行されるプラットフォーム
- [[dbt]] — ELT の変換（T）を担う実質標準ツール
- [[Snowflake]] — ELT のターゲット DWH の代表例
- [[Databricks]] — Delta Lake ベースの ELT・レイクハウス基盤
- [[ETL]] — 変換をロード前に行う従来型パターン
- [[リバースETL]] — DWH からビジネスツールへとデータを配信する逆方向パターン
- [[データエンジニア]] — ELT パイプラインを構築・運用する担当者

## 参考

- [What is ELT? - Databricks](https://www.databricks.com/blog/what-is-elt)
- [Understanding ELT: extract, load, transform - dbt Labs](https://www.getdbt.com/blog/extract-load-transform)
- [ETL vs ELT: Key differences explained - dbt Labs](https://www.getdbt.com/blog/etl-vs-elt)
- [What is ELT? - Google Cloud](https://cloud.google.com/discover/what-is-elt)
