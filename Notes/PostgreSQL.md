---
title: "PostgreSQL"
date: 2026-02-07
tags:
  - DB
  - SQL
related:
  - "[[SQL]]"
  - "[[データウェアハウス]]"
  - "[[オブジェクトストレージ]]"
  - "[[データ基盤]]"
  - "[[DuckDB]]"
  - "[[NewSQL]]"
---

## 概要

PostgreSQL は、カリフォルニア大学バークレー校で1986年に始まった POSTGRES プロジェクトに起源を持つ、オープンソースのオブジェクトリレーショナルデータベース管理システム（ORDBMS）である。[[SQL]] 標準への高い準拠度（SQL:2023 Core の177機能中170以上に適合）と優れた拡張性を特徴とし、2026年現在も開発者から最も称賛されるデータベースの一つである。

## 詳細

### 歴史

| 年 | 出来事 |
|----|--------|
| **1986年** | Michael Stonebraker がバークレー校で POSTGRES プロジェクトを開始 |
| **1995年** | SQL サポートを追加し Postgres95 として公開 |
| **1996年** | PostgreSQL に改名。コミュニティ主導の開発体制へ |
| **2005年** | PostgreSQL 8.0 — Windows ネイティブサポート |
| **2017年** | PostgreSQL 10 — 論理レプリケーション、宣言的パーティショニング |
| **2025年** | PostgreSQL 18 リリース（2025年9月25日） |

### アーキテクチャ

PostgreSQL はクライアントサーバーモデルを採用している:

- **マスタープロセス（Postmaster）**: クライアント接続を受け付け、接続ごとにバックエンドプロセスをフォーク
- **バックエンドプロセス**: 各クライアント接続を担当し、クエリの解析・最適化・実行を行う
- **共有メモリ**: バッファキャッシュ、WAL バッファなどをプロセス間で共有
- **WAL（Write-Ahead Logging）**: データ変更前にログを書き込むことで、障害時の一貫性回復を保証
- **ストレージ**: データベースごとにディレクトリ、テーブルごとにヒープファイルとして物理格納

### コア機能

| 機能 | 説明 |
|------|------|
| **ACID トランザクション** | 原子性・一貫性・分離性・耐久性を完全保証 |
| **MVCC** | 読み取りと書き込みが相互にブロックしない並行制御。同一行の同時更新のみがブロック対象 |
| **拡張性** | カスタムデータ型、関数、演算子、インデックスメソッド、手続き言語を追加可能 |
| **手続き言語** | PL/pgSQL（組み込み）、Python、Perl、Tcl。拡張で Java、JavaScript、Rust 等も対応 |
| **JSON/JSONB** | 半構造化データのネイティブサポート。JSONB は高速な検索・インデキシングが可能 |
| **全文検索** | 組み込みの全文検索エンジン |
| **地理空間データ** | PostGIS 拡張による地理空間クエリ |
| **外部データラッパー（FDW）** | 外部データソース（他の DB、ファイル、API）にSQL でアクセス |

### 他の DB ツールとの比較

| 観点 | PostgreSQL | MySQL | Oracle | SQLite |
|------|-----------|-------|--------|--------|
| ライセンス | PostgreSQL License（自由） | GPL / 商用デュアル | 商用（有料） | パブリックドメイン |
| SQL 準拠 | 非常に高い（170/177） | 中程度 | 高い | 限定的 |
| MVCC | あり | InnoDB のみ | あり | なし（ファイルロック） |
| 拡張性 | 非常に高い | 限定的 | プラグイン | 限定的 |
| JSON サポート | JSONB（高速） | JSON 型 | JSON 型 | JSON 関数 |
| 読み取り性能 | 高い | 非常に高い | 非常に高い | 高い（単一接続） |
| 書き込み性能 | 高い | 中〜高 | 非常に高い | 低い（同時書き込み） |
| 向いている用途 | エンタープライズ、複雑なクエリ、地理空間 | Web アプリ、CMS | 大規模基幹系 | 組み込み、モバイル |
| コスト | 無料 | 無料（Community）/ 有料（Enterprise） | 高価 | 無料 |

### オブジェクトストレージとの使い分け

PostgreSQL などの RDBMS と[[オブジェクトストレージ]]は相互補完の関係にある:

| 観点 | PostgreSQL（RDBMS） | [[オブジェクトストレージ]]（S3 等） |
|------|---------------------|-----------------------------------|
| **データ形式** | 構造化データ（テーブル・行・列） | 非構造化データ（画像、動画、ログ、バイナリ） |
| **スキーマ** | スキーマ・オン・ライト（事前定義が必要） | スキーマレス |
| **アクセスパターン** | 複雑なクエリ、結合、トランザクション | キーベースの読み書き |
| **スケーラビリティ** | 垂直スケールが中心（大規模は Citus 等で水平スケール） | 水平スケールが容易、ペタバイト規模に対応 |
| **コスト** | コンピュートコストが高い | ストレージコストが非常に安い |
| **一貫性** | 強い一貫性（ACID） | 結果整合性（Eventually Consistent） |
| **主な用途** | トランザクション処理、マスタデータ管理、アプリケーション DB | データレイク、バックアップ、メディア格納、ログ保存 |

**併用パターン**:
- PostgreSQL にトランザクションデータやマスタデータを格納し、大容量の非構造化データはオブジェクトストレージに保存
- `aws_s3` 拡張を使い、S3 のデータを PostgreSQL にインポート・エクスポート
- 分析用途ではオブジェクトストレージ上の[[Apache Parquet]]ファイルを[[DuckDB]]や Trino でクエリする方が効率的

### PostgreSQL 18 の注目機能（2025年9月リリース）

- **非同期 I/O サブシステム**: 複数の読み取りリクエストをキューイングし、シーケンシャルスキャンやバキュームを高速化
- **スキップスキャン**: 複合 B-tree インデックスのルックアップ最適化
- **OR 条件の最適化**: WHERE 句の OR 条件でインデックスを活用可能に
- **GIN インデックスの並列構築**: B-tree、BRIN に続き GIN もパラレルビルド対応
- **uuidv7() 関数**: タイムスタンプ順の UUID を生成
- **仮想生成列**: 読み取り時に値を計算する生成列がデフォルトに
- **RETURNING の強化**: INSERT/UPDATE/DELETE/MERGE で OLD/NEW の参照が可能
- **テンポラル制約**: 範囲に対する PRIMARY KEY、UNIQUE、FOREIGN KEY 制約
- **OAuth 認証**: 認証メカニズムの拡張
- **ワイヤプロトコル v3.2**: 2003年以来初のプロトコル更新

## ポイント

- SQL 標準への準拠度が最も高いオープンソース RDBMS（SQL:2023 Core の170/177機能）
- MVCC による高い並行性と ACID トランザクションの完全保証
- 拡張性が極めて高く、PostGIS（地理空間）、JSONB（半構造化データ）など多様なユースケースに対応
- MySQL は読み取り性能とシンプルさ、Oracle は大規模エンタープライズ、SQLite は組み込み用途にそれぞれ強み
- オブジェクトストレージとは相互補完の関係で、構造化データは PostgreSQL、非構造化データはオブジェクトストレージに格納する使い分けが基本

## 関連項目

- [[SQL]] - PostgreSQL が準拠する標準クエリ言語
- [[DuckDB]] - PostgreSQL と同じ SQL 構文でローカル分析に特化
- [[データウェアハウス]] - PostgreSQL をベースとした分析用途
- [[オブジェクトストレージ]] - PostgreSQL と相互補完するデータ格納先
- [[データ基盤]] - PostgreSQL を含むデータアーキテクチャ全体
- [[NewSQL]] - PostgreSQL 互換の分散 SQL（CockroachDB、YugabyteDB）

## 参考

- [PostgreSQL 公式サイト](https://www.postgresql.org/)
- [PostgreSQL: A Brief History of PostgreSQL](https://www.postgresql.org/docs/current/history.html)
- [PostgreSQL 18 Released!](https://www.postgresql.org/about/news/postgresql-18-released-3142/)
- [Neon - PostgreSQL 18 New Features](https://neon.com/postgresql/postgresql-18-new-features)
- [Bytebase - Postgres vs. MySQL: a Complete Comparison in 2026](https://www.bytebase.com/blog/postgres-vs-mysql/)
- [DigitalOcean - SQLite vs MySQL vs PostgreSQL](https://www.digitalocean.com/community/tutorials/sqlite-vs-mysql-vs-postgresql-a-comparison-of-relational-database-management-systems)
