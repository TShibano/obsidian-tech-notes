---
title: "SQL"
date: 2026-02-07
tags:
  - DB
  - SQL
related:
  - "[[データウェアハウス]]"
  - "[[データレイクハウス]]"
  - "[[Apache Iceberg]]"
  - "[[PostgreSQL]]"
  - "[[DuckDB]]"
---

## 概要

SQL（Structured Query Language）は、リレーショナルデータベース管理システム（RDBMS）のデータを操作・管理するためのドメイン固有言語である。1970年代に IBM で開発され、1986年に ANSI 標準、1987年に ISO 標準となり、現在もデータベースの世界で最も広く使われる言語として君臨している。

## 詳細

### 歴史

- **1970年**: Edgar F. Codd がリレーショナルモデルを提唱
- **1974年**: IBM の Donald D. Chamberlin と Raymond F. Boyce が SEQUEL（Structured English Query Language）を開発。System R プロジェクトの一環
- **1979年**: Relational Software（後の Oracle）が初の商用 SQL 製品をリリース
- **1986年**: ANSI SQL 標準化（SQL-86）
- **1987年**: ISO 標準化
- **1992年**: SQL-92（SQL2）— 主要な機能拡張
- **1999年**: SQL:1999 — 正規表現、再帰クエリ、トリガー
- **2003年**: SQL:2003 — XML 対応、ウィンドウ関数
- **2011年**: SQL:2011 — テンポラルデータ
- **2016年**: SQL:2016 — JSON 対応
- **2023年**: SQL:2023 — グラフクエリ、JSON の強化

### SQL のサブ言語

| サブ言語 | 用途 | 主なコマンド |
|----------|------|-------------|
| **DDL（Data Definition Language）** | データベース構造の定義 | `CREATE`, `ALTER`, `DROP`, `TRUNCATE` |
| **DML（Data Manipulation Language）** | データの操作 | `SELECT`, `INSERT`, `UPDATE`, `DELETE` |
| **DCL（Data Control Language）** | アクセス権限の管理 | `GRANT`, `REVOKE` |
| **TCL（Transaction Control Language）** | トランザクション管理 | `COMMIT`, `ROLLBACK`, `SAVEPOINT` |

### 主要な RDBMS

#### オープンソース

| RDBMS | 特徴 | 主なユースケース |
|-------|------|-----------------|
| **[[PostgreSQL]]** | SQL 標準への高い準拠（SQL:2011 の179機能中160対応）。MVCC、拡張性。2026年も最も称賛される DB | エンタープライズ、地理空間データ、複雑なクエリ |
| **MySQL** | 高速・シンプル。世界で最も普及したオープンソース RDBMS | Web アプリケーション、CMS、LAMP スタック |
| **MariaDB** | MySQL からのフォーク。互換性維持しつつ独自機能を追加 | MySQL の代替 |
| **SQLite** | サーバーレス、組み込み型。単一ファイルで動作 | モバイルアプリ、IoT、ローカルアプリケーション |

#### 商用

| RDBMS | 提供元 | 特徴 |
|-------|--------|------|
| **Oracle Database** | Oracle | エンタープライズ向け最大シェア。高可用性、パーティショニング |
| **SQL Server** | Microsoft | Windows エコシステムとの統合。2025版で AI 機能（ベクトル型、ベクトル検索）を内蔵 |
| **IBM Db2** | IBM | メインフレームから始まった歴史ある RDBMS |

#### クラウド DWH / 分析用 SQL エンジン

| ツール | 特徴 |
|--------|------|
| **BigQuery** | Google Cloud のサーバーレス分析エンジン。ペタバイト規模に対応 |
| **Snowflake** | マルチクラウド対応の DWH。ストレージとコンピュート分離 |
| **Amazon Redshift** | AWS エコシステムとの統合に優れた DWH |
| **Databricks SQL** | [[データレイクハウス]]上の SQL 分析エンジン |

#### 分散 SQL クエリエンジン

| ツール | 特徴 |
|--------|------|
| **Trino** | 分散 SQL クエリエンジン。複数データソースへのフェデレーテッドクエリ。インタラクティブ分析に強み |
| **Apache Spark SQL** | 汎用分散処理エンジンの SQL インタフェース。大規模バッチ処理と反復処理に適する |
| **[[DuckDB]]** | インプロセス分析データベース。シングルマシンで高速なローカル分析。100GB 以下のデータに最適 |
| **Apache Hive** | Hadoop 上の SQL インタフェース。大規模バッチ処理向け |

### SQL Server 2025 の注目機能（2026年）

- **ネイティブベクトル型**: AI アプリケーション向けのベクトルデータ型とベクトル検索を内蔵
- **JSON ネイティブ対応**: 1行あたり最大 2GB の JSON データをネイティブ格納
- **正規表現サポート**: T-SQL に正規表現が組み込まれ、複雑なテキスト処理がDB内で完結
- **REST API 内蔵**: エンジン組み込みの REST API でアプリケーション連携が容易に
- **TLS 1.3 標準**: セキュリティ強化、暗号化がデフォルト有効

### SQL の分類と選定指針

| ユースケース | 推奨ツール |
|-------------|-----------|
| Web アプリケーションの OLTP | [[PostgreSQL]]、MySQL |
| 組み込み・モバイル | SQLite |
| 大規模 BI・分析（クラウド） | BigQuery、Snowflake、Redshift |
| データレイク上の分析 | Trino、Spark SQL、[[DuckDB]] |
| ローカルデータ分析 | [[DuckDB]]、SQLite |
| エンタープライズ基幹系 | Oracle、SQL Server |

## ポイント

- SQL は1970年代に IBM で誕生し、RDBMS の標準言語として50年以上使われ続けている
- DDL、DML、DCL、TCL の4つのサブ言語で構成される
- オープンソース（PostgreSQL、MySQL、SQLite）から商用（Oracle、SQL Server）まで多様な実装がある
- クラウド DWH（BigQuery、Snowflake）や分散クエリエンジン（Trino、Spark SQL）により、ペタバイト規模の分析にも対応
- SQL Server 2025 でベクトル型・AI 連携が標準搭載され、SQL と AI の融合が進行中

## 関連項目

- [[PostgreSQL]] - SQL 標準への準拠が最も高いオープンソース RDBMS
- [[DuckDB]] - インプロセス分析向け SQL エンジン
- [[データウェアハウス]] - SQL による分析の主要な基盤
- [[データレイクハウス]] - SQL エンジンによるデータレイク上の分析
- [[Apache Iceberg]] - SQL エンジンからクエリ可能なテーブルフォーマット

## 参考

- [Wikipedia - SQL](https://en.wikipedia.org/wiki/SQL)
- [IBM - What is Structured Query Language (SQL)?](https://www.ibm.com/think/topics/structured-query-language)
- [AWS - What is SQL?](https://aws.amazon.com/what-is/sql/)
- [DigitalOcean - SQLite vs MySQL vs PostgreSQL](https://www.digitalocean.com/community/tutorials/sqlite-vs-mysql-vs-postgresql-a-comparison-of-relational-database-management-systems)
- [Microsoft Learn - What's New in SQL Server 2025](https://learn.microsoft.com/en-us/sql/sql-server/what-s-new-in-sql-server-2025)
- [Bytebase - Postgres vs. MySQL: a Complete Comparison in 2026](https://www.bytebase.com/blog/postgres-vs-mysql/)
