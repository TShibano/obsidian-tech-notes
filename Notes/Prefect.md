---
title: "Prefect"
date: 2026-05-06
tags:
  - DevOps
  - CI/CD
  - Language
  - Python
related:
  - "[[Apache Airflow]]"
  - "[[Dagster]]"
  - "[[DataOps]]"
  - "[[Pipeline as Code]]"
  - "[[モダンデータスタック]]"
---

## 概要

Prefect は Python ネイティブのワークフローオーケストレーションフレームワーク．データパイプライン・ML パイプライン・自動化タスクを，通常の Python コードとして記述できる点が特徴．DAG 定義を強制せず，デコレーターベースで既存コードに最小限の変更でオーケストレーション機能を追加できる．

## 詳細

### 基本概念

| 概念 | 説明 |
|------|------|
| **Flow** | ワークフローの主単位．タスクの実行順序と依存関係を管理 |
| **Task** | 処理の最小単位（アトミックな操作）．`@task` デコレーターで定義 |
| **Deployment** | Flow のスケジューリングと実行設定 |
| **Work Pool** | 実行インフラの抽象化（ローカル・Kubernetes・Docker 等） |

### コード例

```python
from prefect import flow, task

@task
def extract_data(source: str) -> list:
    return [...]

@task
def transform(data: list) -> list:
    return [...]

@flow
def etl_pipeline(source: str):
    data = extract_data(source)
    result = transform(data)
    return result
```

通常の Python 関数にデコレーターを付けるだけでオーケストレーション機能が有効になる．

### [[Apache Airflow]] との比較

| 観点 | Prefect | Airflow |
|------|---------|---------|
| 記述スタイル | 通常の Python コード | DAG 定義ファイル |
| セットアップ | 軽量・シンプル | 重厚（Scheduler・Webserver・DB 必要） |
| 動的パイプライン | 容易（Python で制御） | 制限あり |
| エコシステム | 成長中 | 成熟・広大 |
| 適性 | ML・データサイエンス寄り | 大規模バッチ処理 |

### Prefect Cloud / Prefect OSS

- **Prefect OSS**: OSSとしてセルフホスト可能
- **Prefect Cloud**: マネージドサービス（Prefect Technologies 提供）．UI・アラート・RBAC 機能を提供

### 2025年の動向

- AI・ML ワークフローオーケストレーションの需要増に合わせ採用が拡大
- MLOps コミュニティでの活用（MLOps Zoomcamp 2025 で教材として採用）

## ポイント

- DAG を強制しない「Python as Pipeline」設計により学習コストが低い
- 既存の Python スクリプトを最小変更でオーケストレーション化できる
- [[モダンデータスタック]] のオーケストレーション層で [[Apache Airflow]]・[[Dagster]] と並ぶ選択肢
- ローカル実行→Kubernetes へのシームレスなスケールアップが可能

## 関連項目

- [[Apache Airflow]]
- [[Dagster]]
- [[DataOps]]
- [[Pipeline as Code]]
- [[モダンデータスタック]]

## 参考

- [Prefect 公式サイト](https://www.prefect.io/)
- [GitHub - PrefectHQ/prefect](https://github.com/prefecthq/prefect)
- [Prefect in Python: Modern Workflow Orchestration for Data and AI Pipelines | Medium](https://medium.com/@shouke.wei/prefect-in-python-i-modern-workflow-orchestration-for-data-and-ai-pipelines-70a6add21b7a)
- [AI Workflow Orchestration with Python: Comparing Prefect and Airflow | Medium](https://medium.com/@pysquad/ai-workflow-orchestration-with-python-comparing-prefect-and-airflow-8c28857d740c)
