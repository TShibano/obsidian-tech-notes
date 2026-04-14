---
title: "Dagster"
date: 2026-04-14
tags:
  - DevOps
  - Cloud
  - Tool
related:
  - "[[Apache Airflow]]"
  - "[[dbt]]"
  - "[[DataOps]]"
  - "[[データリネージ]]"
  - "[[データ品質]]"
  - "[[データ基盤]]"
---

## 概要

Dagster はデータアセット中心の宣言的プログラミングモデルを特徴とするオープンソースのデータオーケストレーター。Software-Defined Assets（SDA）という概念により、タスクの実行順序ではなく「どのデータアセットが存在すべきか」を定義するアプローチを採用し、データリネージ・品質チェック・可観測性を組み込みで提供する。

## 詳細

### コアコンセプト: Software-Defined Assets（SDA）

Dagster の最大の特徴は **Software-Defined Assets**（ソフトウェア定義アセット）という概念。データアセット（テーブル、ファイル、ML モデル等）を一級市民として扱い、「何を作るか」と「どう作るか」を一体で定義する。

```python
from dagster import asset

@asset
def raw_orders():
    """注文データをソースから取得"""
    return fetch_orders_from_api()

@asset
def cleaned_orders(raw_orders):
    """注文データをクレンジング"""
    return clean(raw_orders)

@asset
def order_metrics(cleaned_orders):
    """注文メトリクスを集計"""
    return aggregate_metrics(cleaned_orders)
```

この定義から Dagster は自動的に：
- **アセットグラフ**（依存関係の DAG）を構築
- **データリネージ**を追跡
- **マテリアライゼーション**（アセットの実体化）を管理

### Apache Airflow との比較

| 観点 | Dagster | Apache Airflow |
|------|---------|----------------|
| **パラダイム** | アセット中心（何を作るか） | タスク中心（何を実行するか） |
| **データリネージ** | 組み込みでアセットレベルの追跡 | 限定的（タスク間の依存のみ） |
| **データ品質** | ネイティブサポート（Asset Checks） | 外部ツール（Great Expectations 等）が必要 |
| **型システム** | Op・アセットに型定義可能。入出力の検証 | Python 型ヒントのみ |
| **テスタビリティ** | ローカルで軽量にユニットテスト可能 | Airflow コンテキストのモックが必要 |
| **ローカル開発** | 軽量な開発サーバー | 比較的重い環境セットアップ |
| **成熟度** | 新興だが急成長中 | 業界標準。巨大なエコシステム |
| **生産性** | 公称で Airflow の2倍（Dagster 社調べ） | 広い採用実績と豊富な情報 |

### アーキテクチャ

Dagster のアーキテクチャは以下のコンポーネントで構成される：

| コンポーネント | 役割 |
|---------------|------|
| **Dagster Webserver/UI** | GraphQL ベースの Web UI。アセットグラフの可視化、ラン管理、メタデータ閲覧 |
| **Dagster Daemon** | スケジュール実行、センサー、バックフィルの管理 |
| **Code Location** | ユーザーのアセット・ジョブ定義を含むコードリポジトリ |
| **Run Launcher** | ランの実行環境を管理（Docker、K8s、プロセス等） |
| **Instance Storage** | ラン履歴・イベントログの永続化（PostgreSQL 推奨） |

デプロイ構成は柔軟で、ローカル開発からKubernetes、Dagster Cloud（マネージドサービス）まで対応する。

### 主要機能

#### パーティショニング

時系列データの分割管理をネイティブサポート。日次・月次などのパーティションを定義し、特定期間のバックフィル（再計算）を容易に実行できる。

#### センサー

外部イベント（新規ファイルの到着、API の更新等）を検知してパイプラインを自動起動する仕組み。イベント駆動型のワークフローを構築可能。

#### Asset Checks

アセットに対するデータ品質チェックをネイティブに定義。パーティション単位での品質検証も可能（v1.13+）。

#### リソースと設定

外部接続（DB、クラウドサービス等）をリソースとして抽象化し、環境ごとに差し替え可能。テスト時にモックリソースを注入できる。

### インテグレーション

Dagster は主要なデータツールとの統合を提供：

| カテゴリ | ツール |
|---------|--------|
| **変換** | [[dbt]]、Spark、Pandas |
| **DWH** | [[Snowflake]]、BigQuery、Databricks |
| **インジェスト** | Fivetran、Airbyte |
| **BI** | Tableau、Looker |
| **ML** | MLflow、SageMaker |
| **インフラ** | Docker、Kubernetes、AWS、GCP |

特に dbt との統合が強力で、dbt モデルを Dagster アセットとして自動的にインポートし、リネージを統合管理できる。

### 最新動向（2026年）

- **Dagster 1.13.0**（2026年4月）: 最新安定版
- **AI 支援開発**: Claude Code、OpenAI Codex 向けの AI スキル集（dagster-io/skills）を公開。MCP サーバー統合により AI アシスタントから Dagster Cloud を操作可能に
- **Virtual Assets**（プレビュー）: DB ビューのように上流の変更を自動反映するアセットをモデリング
- **State-backed Components**: dbt、Fivetran、Airbyte 等の外部メタデータ依存の統合がデフォルトで有効に
- **Python 3.9 サポート終了**: 最小対応バージョンは Python 3.10 以上

### ユースケース

| ユースケース | 説明 |
|-------------|------|
| **ELT パイプライン** | ソースからの抽出 → DWH へのロード → dbt での変換を一元管理 |
| **ML パイプライン** | 特徴量生成 → モデル訓練 → デプロイを SDA で定義 |
| **データ品質管理** | Asset Checks による品質検証をパイプラインに組み込み |
| **リバースETL** | DWH からの SaaS ツールへのデータ同期 |
| **マルチチーム運用** | チームごとにアセットグラフのサブセットを所有・管理 |

## ポイント

- **アセット中心のパラダイム**: タスクではなくデータアセットを起点に考える。これにより[[データリネージ]]と[[データ品質]]が自然にパイプラインに組み込まれる
- **開発者体験の重視**: ローカル開発・テスト・型安全性が充実。Pythonic な API 設計
- **dbt との強力な統合**: dbt モデルをアセットとしてインポートし、変換前後のリネージを統合管理
- **Airflow からの移行パス**: Airflow DAG を Dagster に段階的に移行するツールを提供
- **Dagster Cloud**: マネージドサービスとしても利用可能で、運用負荷を大幅に削減

## 関連項目

- [[Apache Airflow]] — タスク中心の従来型オーケストレーター。Dagster の主要な比較対象
- [[dbt]] — ELT の変換層。Dagster との統合でリネージを一元管理
- [[DataOps]] — Dagster が推進するデータ運用のプラクティス
- [[データリネージ]] — SDA により自動追跡されるデータの系譜
- [[データ品質]] — Asset Checks によるネイティブな品質管理
- [[データ基盤]] — Dagster がオーケストレーション層として機能するデータ基盤

## 参考

- [Dagster 公式サイト](https://dagster.io/)
- [Dagster 公式ドキュメント](https://docs.dagster.io/)
- [GitHub: dagster-io/dagster](https://github.com/dagster-io/dagster)
- [Dagster Blog: What Are Software-Defined Assets?](https://dagster.io/blog/software-defined-assets)
- [Dagster Blog: Dagster vs Airflow](https://dagster.io/blog/dagster-airflow)
- [Dagster 1.12: Refinement and Acceleration](https://dagster.io/blog/dagster-1-12-monster-mash)
