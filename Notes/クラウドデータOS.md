---
title: "クラウドデータOS"
date: 2026-05-06
tags:
  - Cloud
  - DB
  - DevOps
related:
  - "[[Databricks]]"
  - "[[Snowflake]]"
  - "[[データウェアハウス]]"
  - "[[データ基盤]]"
  - "[[データレイクハウス]]"
  - "[[モダンデータスタック]]"
---

## 概要

クラウドデータOSとは，クラウド上でデータの収集・蓄積・処理・分析・ガバナンスを統合的に提供するプラットフォームの概念．従来は複数のツールを組み合わせて実現していた機能を，単一プラットフォームがOS的に統括する．DatabricksやSnowflakeが代表例．

## 詳細

### 概念の背景

モダンデータスタックが多数のツールで構成されるのに対し，クラウドデータOSはそれらの機能を一つのプラットフォームに垂直統合する方向性を指す．OSがアプリケーションとハードウェアを抽象化するように，データOSはデータインフラを抽象化する．

### 提供される主な機能

| 機能カテゴリ | 内容 |
|-------------|------|
| ストレージ | データレイク・DWH 統合，オブジェクトストレージ管理 |
| コンピューティング | 分散処理エンジン（Spark など），SQL クエリエンジン |
| オーケストレーション | ジョブスケジューリング，ワークフロー管理 |
| ガバナンス | アクセス制御，データカタログ，リネージ管理 |
| ML/AI | モデル学習・デプロイ（MLflow など） |
| 共有・マーケットプレイス | データセット共有，サードパーティ連携 |

### 代表プラットフォームの比較

| 項目 | Databricks | Snowflake |
|------|-----------|-----------|
| 設計思想 | Lakehouse（オープン）優先，ML/AI 中心 | DWH をクラウドネイティブに再発明，分析の使いやすさ |
| コア技術 | Apache Spark，Delta Lake | 独自カラム型エンジン，Dremel 系 |
| オープン度 | OSS 主体（Delta Lake, MLflow） | 独自実装が中心 |
| 強み | AI/ML ワークロード，データエンジニアリング | BI 分析，SQL ユーザー，マルチクラウド |

## ポイント

- 「データOS」という名称はメタファーであり，OSS 標準 vs 独自実装のトレードオフが存在する
- DatabricksとSnowflakeは競合しつつも機能が相互に近づいてきている（Snowflake も ML 機能を拡充）
- クラウドデータOS 採用により運用コストは下がるが，ベンダーロックインリスクが増す

## 関連項目

- [[Databricks]]
- [[Snowflake]]
- [[データウェアハウス]]
- [[データレイクハウス]]
- [[データ基盤]]
- [[モダンデータスタック]]
- [[BigQuery]]

## 参考

- [DatabricksとSnowflakeは何が根本的に違うのか？](https://qiita.com/ktdatascience/items/a2a849bd360cec024a81)
- [DatabricksとSnowflakeの違いとは？AI時代のデータ基盤を比較](https://arpable.com/artificial-intelligence/databricks-complete-guide/)
- [Databricks vs. Snowflake | Databricks](https://www.databricks.com/databricks-vs-snowflake)
