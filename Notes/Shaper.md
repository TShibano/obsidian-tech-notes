---
title: "Shaper"
date: 2026-02-18
tags:
  - DB
  - Tool
related:
  - "[[DuckDB]]"
---

## 概要

Shaper は [[DuckDB]] を基盤とするオープンソースの SQL 駆動データダッシュボードツールである。SQL を書くだけでインタラクティブなダッシュボードを構築でき、単一 Docker コンテナとして動作するため、導入・運用が容易である。

## 詳細

### 背景

Shaper は Taleshape 社が開発・公開しているオープンソースプロジェクトで、2026年2月に Hacker News のトップ記事として注目を集めた。Metabase や Grafana などの既存 BI ツールの代替として、「すべてを SQL で完結させる」コンセプトを掲げている。

### アーキテクチャ

#### DuckDB を基盤とする設計

Shaper は [[DuckDB]] をクエリエンジンとして採用している。DuckDB の豊富な拡張機能を活用することで、多様なデータソースに対応している。

- **Postgres**: 直接接続してクエリ
- **S3**: オブジェクトストレージ上のファイル
- **Google Sheets**: スプレッドシートデータ
- **HTTP / NATS API**: リアルタイムデータ取り込み

#### SQL ディレクティブによるダッシュボード定義

特殊なコメント形式のディレクティブ（`::LABEL`、`::XAXIS`、`::BARCHART_STACKED` など）を SQL クエリに付与することで、ビジュアライゼーションのタイプや設定を宣言的に定義する。

```sql
-- ダッシュボード定義の例（概念）
SELECT
  month ::XAXIS,
  revenue ::BARCHART_STACKED,
  category ::LABEL
FROM sales_data
```

#### 単一コンテナ運用

Shaper は単一の Docker コンテナとして稼働するため、インフラ管理の手間が少ない。ベンダーロックインを避けながら、SQL ベースのワークフローを維持できる。

### 主な機能

| 機能 | 説明 |
|------|------|
| **インタラクティブフィルタ** | SQL だけで動的フィルタリングを実現 |
| **条件表示** | データ条件によるセクションの表示/非表示 |
| **エクスポート** | CSV・Excel・PDF 形式でのデータ出力 |
| **パスワード保護共有** | 認証付きでダッシュボードを公開 |
| **埋め込み SDK** | JavaScript・React SDK によるアプリ組み込み |
| **JWT 認証** | ユーザーごとのアクセス制御 |
| **スキーマ推論** | データソース接続時の自動スキーマ検出 |

### ユースケース

- **軽量 BI 基盤の構築**: Metabase などの大型 BI ツールを使わず、SQL スキルだけでダッシュボードを構築したい場合
- **データプロジェクトの早期立ち上げ**: 最小限のセットアップで分析基盤を稼働させたい場合
- **アプリへの分析機能埋め込み**: iframeを使わずにネイティブ SDK でダッシュボードを組み込む場合
- **データ変換パイプライン**: スケジュール実行によるデータ取り込みと変換の自動化

### 他ツールとの比較

| 観点 | Shaper | Metabase | Grafana |
|------|--------|----------|---------|
| クエリ言語 | SQL のみ | GUI + SQL | PromQL / SQL など |
| ダッシュボード定義 | SQL ディレクティブ | GUI/YAML | JSON/YAML |
| 主なデータソース | DuckDB 対応全般 | RDBMS 中心 | 監視系・時系列中心 |
| 運用形態 | 単一 Docker | サーバー | サーバー |
| ライセンス | オープンソース | OSS（一部商用） | OSS（一部商用） |

## ポイント

- SQL スキルだけでダッシュボードを構築でき、専用の GUI ツール習得が不要
- [[DuckDB]] の拡張性により、S3・Postgres・Google Sheets など多様なデータソースに対応
- 単一 Docker コンテナで運用でき、ベンダーロックインなし
- JavaScript・React SDK による埋め込み分析でアプリ統合が容易
- 「BI as Code」のコンセプトを体現し、バージョン管理との親和性が高い

## 関連項目

- [[DuckDB]] - Shaper のクエリエンジン。SQL で分析・複数データソース対応

## 参考

- [GitHub - taleshape-com/shaper](https://github.com/taleshape-com/shaper)
- [Shaper 公式ドキュメント](https://taleshape.com/shaper/docs/)
- [Turn Your DuckDB Projects Into Interactive Dashboards](https://taleshape.com/blog/turn-your-duckdb-projects-into-interactive-dashboards/)
