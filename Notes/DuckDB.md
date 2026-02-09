---
title: "DuckDB"
date: 2026-02-07
tags:
  - DB
  - SQL
related:
  - "[[SQL]]"
  - "[[PostgreSQL]]"
  - "[[Polars]]"
  - "[[Apache Parquet]]"
  - "[[Apache Iceberg]]"
  - "[[データレイク]]"
---

## 概要

DuckDB は、インプロセス型の分析特化 [[SQL]] データベースエンジンで、「分析の SQLite」とも呼ばれる。列指向ストレージとベクトル化クエリエンジンにより、サーバー不要で高速な OLAP（オンライン分析処理）を実現する。2018年にオランダ CWI の研究者によって開発が始まり、MIT ライセンスで公開されている。

## 詳細

### 背景と歴史

DuckDB は 2018 年に CWI（Centrum Wiskunde & Informatica）の Mark Raasveldt と Hannes Mühleisen によって作成された。データアナリストが pandas や R で非効率なデータベース操作を再実装している状況に着目し、Python や R に簡単に組み込める高性能な分析データベースを目指して開発された。

- **2018年**: CWI でプロジェクト開始
- **2019年**: SIGMOD でデモ論文を発表
- **2024年**: DuckDB Foundation（独立非営利団体）設立。v1.0 リリース
- **2025年**: v1.4.0 で LTS（Long-Term Support）リリース戦略を導入
- **2026年1月**: v1.4.4 LTS リリース

DuckDB Foundation がプロジェクトの知的財産を保有し、MIT ライセンスでの永続的なオープンソース公開を保証している。

### アーキテクチャ

#### インプロセス実行

DuckDB はアプリケーションと同一プロセス内で動作する。外部サーバーが不要で、データ転送がメモリ空間内で完結するため、ソケット通信によるオーバーヘッドがない。

#### 列指向ストレージ

行指向の[[PostgreSQL]]や SQLite とは異なり、DuckDB はデータを列単位で格納する:

- 分析クエリで必要な列のみをディスクから読み取り
- CPU キャッシュの効率的な利用
- 列単位の圧縮が可能

#### ベクトル化クエリエンジン

「Vector Volcano」モデルを採用。1,024〜2,048 個の値をまとめたベクトル単位で処理し、関数呼び出しのオーバーヘッドを大幅に削減する。行単位で処理する PostgreSQL、MySQL、SQLite と比較して、分析クエリで圧倒的に高いパフォーマンスを実現する。

#### Morsel-Driven 並列処理

データを「Morsel」（小さなチャンク）に分割し、利用可能なすべてのCPUコアで自動的に並列処理する。マルチコア環境を意識した設計により、スケーラビリティが高い。

### 主な特徴

| 特徴 | 説明 |
|------|------|
| **サーバー不要** | インプロセスで動作。インストールと利用が極めて簡単 |
| **SQL 完全対応** | ウィンドウ関数、CTE、サブクエリなど高度な SQL をサポート |
| **多様なデータソース** | [[Apache Parquet]]、CSV、JSON、Excel、[[Apache Iceberg]]、S3 上のファイルを直接クエリ |
| **拡張メカニズム** | 拡張機能でデータ型、関数、ファイルフォーマット、SQL 構文を追加可能 |
| **ゼロ依存** | C++11 コンパイラのみで構築可能。外部依存なし |
| **多言語対応** | Python、R、Java、Node.js、Go、Rust などのバインディングを提供 |
| **Zone Maps** | 各列のmin/max統計を使った選択的スキャンで不要なデータ読み取りを回避 |

### S3 / クラウドストレージとの統合

DuckDB は `httpfs` 拡張により、S3 上の Parquet ファイルを直接 SQL でクエリできる:

- Parquet ファイルの列統計を活用し、データ全体をダウンロードせずにクエリを実行
- Hive パーティション対応で、大規模なパーティションデータの高速クエリが可能
- S3 上の Parquet データのリパーティションも SQL 一文で実行可能
- [[Apache Iceberg]] テーブルへの読み書き対応（v1.4.2 で INSERT/UPDATE/DELETE サポート）

### ユースケース

| ユースケース | 説明 |
|-------------|------|
| **ローカルデータ分析** | ノートPC上で数十〜数百 GB のデータをインタラクティブに分析 |
| **S3 上のデータ調査** | リモートの Parquet/CSV ファイルをダウンロードせずにクエリ |
| **ETL パイプライン** | dbt-DuckDB による軽量なデータ変換パイプライン |
| **データレイクのクエリ** | Iceberg テーブルや Parquet ファイルへの SQL アクセス |
| **アプリケーション組み込み** | 分析機能をアプリケーションに直接組み込み |
| **CI/CD でのデータテスト** | サーバー不要でテスト環境でのデータ検証が容易 |

### Polars との比較

| 観点 | DuckDB | [[Polars]] |
|------|--------|---------|
| インタフェース | SQL | DataFrame API（式ベース） |
| 実行モデル | インプロセス DB | ライブラリ |
| 得意領域 | SQL ベースの分析、複数テーブルの結合 | プログラマティックなデータ変換パイプライン |
| データソース | ファイル、S3、Iceberg を SQL でクエリ | ファイルベースの入出力 |
| 相互連携 | Polars の DataFrame を直接クエリ可能 | DuckDB をバックエンドとして利用可能 |

### 最新動向（2026年）

- **v1.4.4 LTS**（2026年1月リリース）: パフォーマンス改善とバグフィックス
- **Iceberg 完全書き込みサポート**: v1.4.2 で INSERT/UPDATE/DELETE に対応
- **v1.5.0 リリース予定**: C クライアント API と C 拡張 API への移行・ドキュメント強化
- **LTS 戦略**: 1つおきのバージョンを LTS として1年間のコミュニティサポートを提供
- **DuckLake**: DuckDB チームによるレイクハウスフォーマットプロジェクト

## ポイント

- インプロセス型でサーバー不要、「分析の SQLite」として手軽に高速な OLAP を実行可能
- 列指向ストレージ＋ベクトル化クエリエンジンにより、分析クエリで従来の行指向 DB を大幅に上回る性能
- S3 上の Parquet / Iceberg を直接 SQL でクエリでき、データレイクとの親和性が高い
- 拡張メカニズムにより、ファイルフォーマットやデータソースを柔軟に追加可能
- 数十〜数百 GB のローカル分析やクラウドデータの調査に最適。分散処理が必要な規模では Spark や Trino を検討

## 関連項目

- [[SQL]] - DuckDB のクエリ言語
- [[PostgreSQL]] - 行指向 RDBMS。OLTP に強み
- [[Polars]] - DataFrame API ベースの高速分析ライブラリ。相互連携が可能
- [[Apache Parquet]] - DuckDB が直接クエリ可能な列指向ファイルフォーマット
- [[Apache Iceberg]] - DuckDB が読み書き可能なオープンテーブルフォーマット
- [[データレイク]] - DuckDB がクエリエンジンとして機能するデータ基盤

## 参考

- [DuckDB 公式サイト](https://duckdb.org/)
- [Why DuckDB](https://duckdb.org/why_duckdb)
- [GitHub - duckdb/duckdb](https://github.com/duckdb/duckdb)
- [Announcing DuckDB 1.4.4 LTS](https://duckdb.org/2026/01/26/announcing-duckdb-144)
- [endjin - DuckDB in Depth: How It Works and What Makes It Fast](https://endjin.com/blog/2025/04/duckdb-in-depth-how-it-works-what-makes-it-fast)
- [DuckDB S3 Parquet Import](https://duckdb.org/docs/stable/guides/network_cloud_storage/s3_import)
