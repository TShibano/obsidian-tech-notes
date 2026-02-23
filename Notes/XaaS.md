---
title: "XaaS"
date: 2026-02-23
tags:
  - Cloud
related:
  - "[[API]]"
  - "[[データ基盤]]"
---

## 概要

XaaS（Anything as a Service）はクラウドコンピューティングサービスの総称で、X に当てはまる様々なリソースやサービスをインターネット経由で提供するモデル。IaaS・PaaS・SaaS が三大柱であり、FaaS・BaaS・DBaaS など用途特化型も急速に広まっている。

## 詳細

### XaaS の概念

従来のオンプレミス環境では、ハードウェア・OS・ミドルウェア・アプリケーションをすべて自社で管理していた。クラウドの普及により、これらの管理責任範囲をクラウドプロバイダと分担するモデルが一般化し、その分担の粒度によって以下のサービスモデルが定義される。

### 管理責任の分担図

```
オンプレミス   IaaS        PaaS        SaaS
-----------   -----        -----        -----
アプリ        [自社]        [自社]       [提供側]
データ        [自社]        [自社]       [提供側]
ランタイム    [自社]        [提供側]     [提供側]
ミドルウェア  [自社]        [提供側]     [提供側]
OS            [自社]        [提供側]     [提供側]
仮想化        [自社]        [提供側]     [提供側]
サーバー      [自社]        [提供側]     [提供側]
ストレージ    [自社]        [提供側]     [提供側]
ネットワーク  [自社]        [提供側]     [提供側]
```

### 主要サービスモデル

#### IaaS（Infrastructure as a Service）

仮想マシン・ストレージ・ネットワークのハードウェアをクラウドで提供。OS 以上のレイヤーは自社で管理。最も自由度が高いが、管理コストも高い。

**代表サービス:**
- AWS EC2 / S3 / VPC
- Google Compute Engine
- Microsoft Azure Virtual Machines

**適した用途:**
- 独自の OS 設定・ネットワーク構成が必要な場合
- 既存オンプレミスからのリフト&シフト
- 特定のソフトウェア要件がある場合

#### PaaS（Platform as a Service）

アプリケーションをデプロイ・実行するための実行環境（OS・ミドルウェア・ランタイム含む）を提供。インフラ管理なしでコードのデプロイに集中できる。

**代表サービス:**
- Heroku
- Google App Engine
- AWS Elastic Beanstalk
- Render
- Railway

**適した用途:**
- インフラを管理したくない開発チーム
- 素早くアプリを本番環境にデプロイしたい場合
- スタートアップや小規模チーム

#### SaaS（Software as a Service）

完成したアプリケーションをサブスクリプション形式で提供。ユーザーはデータの管理のみを担う。

**代表サービス:**
- Google Workspace（Gmail・Drive・Docs）
- Microsoft 365
- Salesforce
- Slack / Notion / Figma

**適した用途:**
- すぐに使い始めたいビジネスアプリ
- IT リソースが少ない組織

#### FaaS（Function as a Service）/ Serverless

個別の関数単位でコードを実行するモデル。サーバー管理不要、実行時のみ課金。「サーバーレス」と同義で使われることが多い。

**代表サービス:**
- AWS Lambda
- Google Cloud Functions
- Azure Functions
- Cloudflare Workers

**特徴:**
- イベント駆動（HTTP リクエスト・キュー・タイマー等でトリガー）
- コールドスタート問題（初回実行時に遅延が発生）
- ステートレス設計が必要
- 短時間処理に適している（最大実行時間制限あり）

**適した用途:**
- API エンドポイント
- 画像リサイズ・変換処理
- Webhook 受信処理
- バッチ処理・スケジュールジョブ

#### BaaS（Backend as a Service）

バックエンドの共通機能（認証・データベース・ストレージ・プッシュ通知・API など）をまるごとクラウドサービスとして提供。フロントエンド開発者がバックエンドを構築せずに完全なアプリを作れる。

**代表サービス:**
- Firebase（Google）
- Supabase（PostgreSQL ベース、OSS）
- AWS Amplify

**適した用途:**
- モバイルアプリ・SPA のバックエンド
- プロトタイプ・MVP の素早い開発
- フロントエンドエンジニアが中心のチーム

#### DBaaS（Database as a Service）

データベースのインフラ管理（インストール・設定・バックアップ・スケーリング）をクラウドに任せ、接続エンドポイントのみを提供する。

**代表サービス:**
- Amazon RDS / Aurora
- Google Cloud SQL / Spanner / BigQuery
- PlanetScale（MySQL）
- Neon（Serverless PostgreSQL）
- Snowflake（Data Warehouse）

### サービスモデルの選択基準

| 状況 | 推奨モデル |
|------|----------|
| 完全なインフラ制御が必要 | IaaS |
| コードデプロイに集中したい | PaaS |
| 既存 SaaS を使う | SaaS |
| イベント駆動の軽量処理 | FaaS |
| モバイル/SPA のバックエンド | BaaS |
| データベースだけマネージド化 | DBaaS |

### クラウド3大プロバイダの比較

| サービス分類 | AWS | GCP | Azure |
|------------|-----|-----|-------|
| IaaS | EC2 | Compute Engine | Virtual Machines |
| PaaS | Elastic Beanstalk | App Engine | App Service |
| FaaS | Lambda | Cloud Functions | Azure Functions |
| DBaaS（RDBMS） | RDS / Aurora | Cloud SQL | Azure SQL |
| DBaaS（DWH） | Redshift | BigQuery | Synapse Analytics |
| BaaS | Amplify | Firebase | — |

### 最新トレンド（2025〜2026年）

- **AI as a Service（AIaaS）**: LLM API（OpenAI・Anthropic・Google Gemini）がサービス化
- **GPU as a Service**: CoreWeave・Lambda Labs などの GPU クラウド
- **Serverless コンテナ**: AWS Fargate・Google Cloud Run など、コンテナを FaaS 的に使うハイブリッド形態

## ポイント

- XaaS は「管理責任をどこまでクラウドに委ねるか」という連続体
- 自由度: IaaS > PaaS > SaaS / 管理コスト: IaaS > PaaS > SaaS
- FaaS はスケーリング・コスト最適化の手段として普及（AWS Lambda が代表）
- BaaS（Firebase・Supabase）はフルスタック開発のスピードを大幅に向上
- AIaaS の台頭により、モデル学習・推論もクラウドで消費するモデルが主流に

## 関連項目

- [[API]] - クラウドサービスの多くは API 経由で利用する
- [[データ基盤]] - DBaaS・データウェアハウスとのつながり

## 参考

- [IaaS, PaaS, SaaS の違い - IBM](https://www.ibm.com/think/topics/iaas-paas-saas)
- [Cloud Service Models - Google Cloud](https://cloud.google.com/learn/paas-vs-iaas-vs-saas)
- [Cloud Service Models Explained - DataCamp](https://www.datacamp.com/blog/cloud-service-models)
- [IaaS, PaaS, FaaS, SaaS - Microsoft Learn](https://learn.microsoft.com/en-us/training/modules/describe-cloud-service-types/)
