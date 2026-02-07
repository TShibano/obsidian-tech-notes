---
title: "Apache Airflow"
date: 2026-02-07
tags:
  - DevOps
  - Cloud
related:
  - "[[データ基盤]]"
  - "[[データレイク]]"
  - "[[データウェアハウス]]"
  - "[[データカタログ]]"
---

## 概要

Apache Airflow は、データパイプラインのワークフローをプログラムで作成・スケジューリング・監視するためのオープンソースプラットフォームである。2014年に Airbnb で開発が始まり、現在は Apache Software Foundation のトップレベルプロジェクトとして活発にメンテナンスされている。ワークフローを DAG（有向非巡回グラフ）として Python コードで定義し、タスクの依存関係・スケジュール・リトライ・アラートを統合的に管理する。

## 詳細

### DAG（有向非巡回グラフ）

Airflow の中核概念である DAG は、一連のタスクとその依存関係を有向非巡回グラフとして表現する:

- **Task**: ワークフロー内の最小実行単位。Operator（実行ロジック）のインスタンス
- **DAG**: Task の集合とその依存関係の定義。Python コードで記述
- **DAG Run**: DAG の1回の実行インスタンス。スケジュールまたは手動でトリガー
- **Task Instance**: 特定の DAG Run における個々の Task の実行

### 主要コンポーネント

| コンポーネント | 役割 |
|-------------|------|
| **Scheduler** | DAG の解析、タスクのスケジューリング、実行キューへの投入 |
| **Executor** | タスクの実行方式を決定（Local、Celery、Kubernetes 等） |
| **Worker** | 実際にタスクを実行するプロセス |
| **Web Server** | DAG の実行状況をリアルタイムで監視・操作する Web UI |
| **Metadata Database** | DAG・タスクの状態、実行履歴、接続情報を格納（PostgreSQL / MySQL） |

### メリット

- **Python によるワークフロー定義**: Python コードで柔軟にワークフローを記述でき、条件分岐・ループ・変数を自在に活用可能
- **豊富なオペレーター**: AWS、GCP、Azure、DB、Slack 等との統合オペレーターが標準で提供され、外部システムとの連携が容易
- **スケーラビリティ**: CeleryExecutor や KubernetesExecutor により、複数ワーカーでの並列処理が可能
- **監視と可視化**: Web UI でタスクの実行状況、ログ、依存関係をリアルタイムに確認可能
- **リトライとアラート**: タスク失敗時の自動リトライ、アラート通知（メール、Slack 等）を設定可能
- **再現性**: DAG が Python コードで定義されるため、バージョン管理・コードレビュー・テストが可能
- **大規模コミュニティ**: Apache プロジェクトとして最大級のコミュニティ。豊富なドキュメントとプラグイン

### 注意点

- **セットアップの複雑さ**: Scheduler、Web Server、Worker、Metadata DB の構成が必要で、初期構築にはインフラ知識が求められる
- **バッチ処理向き**: リアルタイムストリーミング処理には不向き（バッチ指向の設計）
- **DAG の静的性**: DAG は事前に定義される静的な構造のため、動的に変化するワークフローへの対応はやや制約がある（Airflow 3 で改善）

### Airflow 3（2025年4月 GA）

2025年4月にリリースされた Airflow 3.0 は、プロジェクト史上最大のアップデートとなった:

| 新機能 | 説明 |
|--------|------|
| **DAG バージョニング** | 実行中の DAG は開始時のバージョンで完了。新バージョンがアップロードされても影響を受けない |
| **イベント駆動スケジューリング** | メッセージプロバイダーとの統合により、外部イベントに反応してワークフローをトリガー |
| **リデザインされた UI** | アセット指向とタスク指向のワークフローをシームレスに統合した新 UI |
| **Task SDK** | 多言語対応の Task 実行基盤。Python 以外の言語でのタスク記述を可能に |
| **Human-in-the-Loop（HITL）** | ワークフローを一時停止して人間の判断を待つ機能。AI/ML ワークフロー、承認プロセスに有用（3.1 で導入） |
| **国際化** | 17言語対応（3.1 で導入） |

最新バージョンは **Airflow 3.1.7**（2026年2月4日リリース）。

### 代替ツールとの比較

| 観点 | Apache Airflow | Dagster | Prefect |
|------|---------------|---------|---------|
| **設計思想** | タスク指向。DAG でタスクの依存関係を定義 | アセット（データ）指向。データ資産の変換パイプラインとして定義 | フロー指向。Python デコレーターでシンプルに定義 |
| **動的ワークフロー** | 静的 DAG が基本（3.0 で改善） | 動的パイプライン生成に強み | 動的ワークフロー管理に優れる |
| **開発者体験** | 成熟しているが複雑 | ユニットテスト、CI、ステージング環境に最適化 | シンプルさと迅速なセットアップを重視 |
| **エラー処理** | リトライ、アラート | リトライ、詳細なエラーログ | 堅牢なエラーハンドリング、自動リトライ |
| **エコシステム** | 最大級。豊富なプロバイダーパッケージ | 拡大中。ボイラープレート削減に注力 | クラウドネイティブ設計。モダンな統合 |
| **マネージドサービス** | Amazon MWAA、Cloud Composer、Astronomer | Dagster Cloud | Prefect Cloud |

### ユースケース

- **ETL / ELT パイプライン**: [[データレイク]]から[[データウェアハウス]]へのデータ移行・変換の自動化
- **ML パイプライン**: モデルのトレーニング、評価、デプロイの自動化
- **定期バッチ処理**: 日次・週次のデータ集計、レポート生成
- **データ品質チェック**: データの整合性検証、異常検知の自動化

## ポイント

- Python コードで DAG を定義し、データパイプラインをスケジューリング・監視するオーケストレーションツール
- 豊富なオペレーターとプラグインにより、クラウドサービス・DB・メッセージングなど多様なシステムと統合
- Airflow 3 で DAG バージョニング、イベント駆動スケジューリング、HITL など大幅な機能強化
- [[データ基盤]]のモダンデータスタックにおけるワークフローオーケストレーション層の代表的ツール
- 代替として Dagster（アセット指向）、Prefect（シンプルさ重視）がある

## 関連項目

- [[データ基盤]]
- [[データレイク]]
- [[データウェアハウス]]
- [[データカタログ]]

## 参考

- [Apache Airflow 公式サイト](https://airflow.apache.org/)
- [Apache Airflow 3 GA 発表](https://airflow.apache.org/blog/airflow-three-point-oh-is-here/)
- [Apache Airflow 3.1.0: Human-Centered Workflows](https://airflow.apache.org/blog/airflow-3.1.0/)
- [AWS - Introducing Apache Airflow 3 on Amazon MWAA](https://aws.amazon.com/jp/blogs/news/introducing-apache-airflow-3-on-amazon-mwaa-new-features-and-capabilities/)
- [OpenStandia - Apache Airflowとは？](https://openstandia.jp/oss_info/apache_airflow/)
- [Astronomer - State of Apache Airflow 2026 Report](https://www.prnewswire.com/news-releases/astronomer-releases-state-of-apache-airflow-2026-report-302667480.html)
