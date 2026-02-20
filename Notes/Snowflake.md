---
title: "Snowflake"
date: 2026-02-20
tags:
  - DB
  - Cloud
  - AI
related:
  - "[[データウェアハウス]]"
  - "[[データレイクハウス]]"
  - "[[SQL]]"
  - "[[Delta Lake]]"
  - "[[Apache Iceberg]]"
---

## 概要

Snowflake は、ストレージ・コンピュート・クラウドサービスを独立してスケール可能な3層アーキテクチャを持つフルマネージドのクラウドネイティブデータプラットフォームである。AWS・Azure・GCP のマルチクラウドで稼働し、データウェアハウスとして出発しながら現在は「AI Data Cloud」として AI エージェント・機械学習・データ共有など幅広い機能を提供している。

## 詳細

### アーキテクチャ

Snowflake の最大の特徴は、ストレージとコンピュートを完全に分離した3層構造：

#### 1. データストレージ層

- データは自動的に **マイクロパーティション**（圧縮・暗号化・カラムナー形式）に整理される
- ユーザーが管理するストレージではなく、Snowflake が自動管理するオブジェクトストレージ（S3/Azure Blob/GCS）に保存
- クエリ結果のキャッシュ・スノーフレーク内部の最適化が透過的に行われる

#### 2. クエリ処理層（仮想ウェアハウス）

- **仮想ウェアハウス（Virtual Warehouse）**: クエリを実行する独立した MPP（Massively Parallel Processing）コンピュータクラスタ
- X-Small〜6X-Large まで T-シャツサイズで拡張し、使用した分だけ課金
- 複数のウェアハウスが同一のストレージを独立して参照できる（ワークロード分離）

#### 3. クラウドサービス層

システム全体の「脳」として機能し、以下を担当：
- 認証・アクセス制御
- メタデータ管理
- クエリパーシング・最適化
- インフラ管理

### 主要機能

#### Time Travel & Fail-Safe

- **Time Travel**: 過去90日以内のデータスナップショットにアクセス可能（誤削除・誤更新の復元）
- **Fail-Safe**: Time Travel 期間終了後もさらに7日間のデータ保護

#### データ共有（Data Sharing）

Snowflake Data Marketplace を通じて、データをコピーせずにリアルタイムで他アカウント・組織と共有できる。

#### Iceberg Tables サポート

Snowflake は Apache Iceberg テーブルをネイティブに管理できる。Snowflake がストレージ管理するモードと、外部 Iceberg テーブルを読み取るモードの両方をサポート。

#### ストレージライフサイクルポリシー

データの年齢に応じて WARM → COOL → COLD ストレージ階層に自動移行するルール設定が可能（コスト最適化）。

### Snowflake Cortex AI

2024〜2025年にかけて AI 機能「Cortex」が大幅拡充された：

#### Cortex AI Functions

SQL から直接 LLM を呼び出せる関数群：

```sql
-- テキスト分類の例
SELECT SNOWFLAKE.CORTEX.CLASSIFY_TEXT(review_text, ['positive', 'negative', 'neutral'])
FROM customer_reviews;

-- テキスト要約
SELECT SNOWFLAKE.CORTEX.SUMMARIZE(document_content)
FROM legal_documents;
```

#### Cortex Agents

構造化データ（Cortex Analyst）と非構造化データ（Cortex Search）を横断して情報を収集・分析する自律エージェント。LLM を用いて計画立案・ツール実行・回答生成を行う。2025年11月に GA 化。

#### Cortex Search

非構造化データ（PDF・テキスト・画像等）の意味検索基盤。Cortex Agents のツールとして機能し、ハイブリッド検索（ベクトル + キーワード）をサポート。

#### Snowflake Intelligence

2025年8月にパブリックプレビュー開始のエージェント型 AI フレームワーク。自然言語でデータに問い合わせ・分析・アクションを行えるインターフェースを提供。

#### Cortex Code

2026年2月に発表されたデータ開発向け AI コーディングエージェント。SQL 生成・データパイプライン構築・ML モデル開発を自然言語でサポートし、企業のデータコンテキストを深く理解した提案を行う。

### Snowflake vs 他の DWH・データプラットフォーム

| 観点 | Snowflake | BigQuery | Redshift | Databricks |
|------|-----------|----------|----------|------------|
| ストレージ管理 | フルマネージド | フルマネージド | フルマネージド（Managed Storage） | ユーザー管理（S3等） |
| コンピュート | 仮想ウェアハウス（手動 or 自動スケール） | サーバーレス自動スケール | クラスタ管理 | クラスタ or サーバーレス |
| リアルタイム処理 | Snowpipe Streaming | Dataflow 連携 | Kinesis 連携 | Structured Streaming |
| AI 機能 | Cortex（Agents・Search・Functions） | Gemini（BigQuery ML・Vertex AI） | Bedrock 連携 | MLflow・Unity Catalog |

## ポイント

- ストレージとコンピュートの完全分離により、ETL 処理・BI・ML など異なるワークロードを互いに影響なく実行できる
- Cortex AI の進化により、Snowflake はデータウェアハウスから「AI Data Cloud」へと転換しつつある
- 6,100 以上のアカウントが Cortex を使って AI アプリを構築（2025年末時点）
- Apache Iceberg テーブルのサポートにより Delta Lake との相互運用性も向上
- 従量課金（コンピュートはウェアハウス稼働時間、ストレージはデータ量）のため、適切なウェアハウス停止設定がコスト管理に重要

## 関連項目

- [[データウェアハウス]]
- [[データレイクハウス]]
- [[SQL]]
- [[Delta Lake]]
- [[Apache Iceberg]]

## 参考

- [Snowflake Key Concepts and Architecture](https://docs.snowflake.com/en/user-guide/intro-key-concepts)
- [Snowflake Cortex AI](https://www.snowflake.com/en/product/features/cortex/)
- [Cortex Agents GA (Nov 2025)](https://docs.snowflake.com/en/release-notes/2025/other/2025-11-04-cortex-agents)
- [Snowflake Unveils Cortex Code (Feb 2026)](https://www.snowflake.com/en/news/press-releases/snowflake-unveils-cortex-code-an-ai-coding-agent-that-drastically-increases-productivity-by-understanding-your-enterprise-data-context/)
- [Snowflake 2026 AI Data Cloud Roadmap](https://www.infojiniconsulting.com/blog/the-2026-data-cloud-roadmap-how-snowflake-is-pivoting-from-storage-to-ai-native-data-products/)
