---
title: "dbt"
date: 2026-02-26
tags:
  - DevOps
  - DB
  - SQL
  - Tool
related:
  - "[[データウェアハウス]]"
  - "[[データリネージ]]"
  - "[[データ品質]]"
  - "[[Snowflake]]"
  - "[[Apache Airflow]]"
  - "[[Databricks]]"
  - "[[モダンデータウェアハウス]]"
---

## 概要

dbt（data build tool）はdbt Labs が開発するオープンソースのデータ変換ツール。ELT パイプラインの「T（Transform）」を担当し、クラウドDWH上でSQLによるデータ変換をソフトウェアエンジニアリングのベストプラクティス（バージョン管理・テスト・ドキュメント）と組み合わせて実現する。現代のデータエンジニアリングにおける事実上の標準ツール。

## 詳細

### dbt の役割

dbt は **ELT の T（Transform）** のみを担当する。データの抽出（E）や読み込み（L）は行わず、すでにDWHに読み込まれた生データを分析可能な形式に変換することに特化している。

```
[ソース] → Extract → Load → [DWH: 生データ] → dbt Transform → [DWH: 分析用データ]
                                                                      ↑
                                                               ここを担当
```

### 動作の仕組み

- SQL ファイル（`.sql`）を書くだけで、dbt が `CREATE TABLE AS SELECT` や `INSERT INTO` などの DDL/DML を自動生成してDWH上で実行
- Python でのモデル定義にも対応（dbt-python）
- Snowflake, BigQuery, Redshift, Databricks, DuckDB など主要DWHに対応

### 主要機能

#### モデル（Models）

変換ロジックを SQL ファイルとして管理。`ref()` 関数で他モデルへの依存関係を宣言することで、DAG（有向非巡回グラフ）として依存関係を自動管理。

```sql
-- models/orders_summary.sql
SELECT
  customer_id,
  COUNT(*) AS order_count,
  SUM(amount) AS total_amount
FROM {{ ref('stg_orders') }}  -- stg_orders モデルを参照
GROUP BY customer_id
```

#### テスト（Tests）

データ品質を自動検証。`schema.yml` に定義するだけでテストを実行できる。

```yaml
# models/schema.yml
models:
  - name: stg_orders
    columns:
      - name: order_id
        tests:
          - not_null
          - unique
      - name: status
        tests:
          - accepted_values:
              values: ['placed', 'shipped', 'completed']
```

カスタムテストも SQL で記述可能。

#### ドキュメント

`dbt docs generate` で依存関係グラフ付きのドキュメントを自動生成。カラムの説明を YAML に記述するだけで、整備されたデータカタログとして機能する。

#### データリネージ

`ref()` の依存関係から自動的にリネージグラフを生成。上流のモデル変更が下流にどう影響するかを視覚化できる。

### dbt Core vs dbt Cloud

| 項目 | dbt Core | dbt Cloud |
|------|---------|-----------|
| 形態 | OSS CLI ツール | SaaS プラットフォーム |
| スケジューリング | 別途 Airflow 等が必要 | 組み込みスケジューラ |
| Web UI | なし | IDE・ドキュメント・ジョブ管理 |
| 料金 | 無料 | 有料（Developerプランは無料）|

### Jinja テンプレート

SQL 内で Jinja2 テンプレートが使え、動的なSQL生成が可能。

```sql
{% for payment_method in ['credit_card', 'bank_transfer'] %}
  SUM(CASE WHEN payment_method = '{{ payment_method }}' THEN amount END) AS {{ payment_method }}_amount,
{% endfor %}
```

## ポイント

- **Analytics Engineer の誕生**: dbt は「Analytics Engineer」という職種・概念を生み出した。SQLが書けるアナリストがソフトウェアエンジニアリングの手法でデータを管理する
- **コードとしてのデータ変換**: Git でバージョン管理し、PRレビューし、CIでテストを走らせる
- **DWH上で実行**: データをDWHの外に出さないため、セキュリティと性能面で有利
- **ドキュメントとリネージの自動生成**: 手動で作成・維持するコストが大幅に削減される
- **Airflow との関係**: スケジューリングは Airflow や Prefect に任せ、dbt はトランスフォームに集中する設計が多い

## 関連項目

- [[データウェアハウス]] — dbt は DWH 上のデータ変換ツールとして機能
- [[データリネージ]] — dbt は自動的にリネージグラフを生成する
- [[データ品質]] — dbt tests でデータ品質の自動検証を実現
- [[Snowflake]] — dbt + Snowflake は最も普及した組み合わせのひとつ
- [[Apache Airflow]] — Airflow で dbt ジョブをスケジューリングする構成が多い
- [[Databricks]] — dbt + Databricks の統合も進んでいる

## 参考

- [dbt Labs: What exactly is dbt?](https://www.getdbt.com/blog/what-exactly-is-dbt)
- [dbt Labs: Best ELT Tools](https://www.getdbt.com/blog/best-elt-tools)
