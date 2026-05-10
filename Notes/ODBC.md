---
title: "ODBC"
date: 2026-05-10
tags:
  - DB
  - Tool
related:
  - "[[JDBC]]"
  - "[[SQL]]"
  - "[[リレーショナルデータベース]]"
---

## 概要

ODBC（Open Database Connectivity）は，アプリケーションからデータベースに接続するための標準API仕様．Microsoftが1992年に策定し，DBMS（データベース管理システム）の種類に依存しない統一インターフェースを提供する．C言語向けAPIを主体とし，WindowsとUNIX/Linux両環境で利用される．

## 詳細

### アーキテクチャ

```
アプリケーション
      ↓ ODBC API 呼び出し
ドライバマネージャ（Driver Manager）
      ↓ DSN またはドライバ情報に基づいてドライバを選択
ODBCドライバ（各DBMS用）
      ↓ DBMS ネイティブプロトコル
データベースサーバ
```

- **ドライバマネージャ**: ODBCドライバのロード・管理，接続情報の解決を担当（Windows: odbcad32.exe）
- **ODBCドライバ**: 各DBMS（MySQL，PostgreSQL，SQL Server，Oracle等）が提供するライブラリ
- **DSN（Data Source Name）**: 接続情報を定義した名前付き設定（System DSN / User DSN / File DSN）

### 主な操作フロー

1. `SQLConnect` / `SQLDriverConnect` で接続確立
2. `SQLAllocHandle` でステートメントハンドル確保
3. `SQLPrepare` → `SQLExecute` （または `SQLExecDirect`） でSQL実行
4. `SQLFetch` で結果セット取得
5. `SQLDisconnect` で切断

### [[JDBC]] との比較

| 比較項目 | ODBC | [[JDBC]] |
|---------|------|-------|
| 対象言語 | C/C++（他言語はブリッジ経由） | Java |
| プラットフォーム | Windows / Linux / macOS | JVM 上（クロスプラットフォーム） |
| 策定者 | Microsoft | Sun Microsystems（現 Oracle） |
| ドライバ管理 | OS 組み込みのドライバマネージャ | JVM ランタイムで管理 |
| パフォーマンス | ネイティブコードのため高速 | JVM オーバーヘッドあり |
| 主な用途 | Excel，Power BI，BI ツール連携 | Java アプリケーション |

### BIツールでの活用

Power BI，Tableau，Excel，Looker 等の多くのBIツールはODBCを標準のDB接続手段として採用．ODBCドライバが提供されていればどのDBMSにも接続できる汎用性が高い．

## ポイント

- ODBC ドライバはほとんどのDBMSで無償提供されている
- Python の `pyodbc` ライブラリを使うと，Python スクリプトから ODBC 経由で各種DBに接続可能
- BIツールからクラウドDWH（Snowflake，BigQuery，Redshift等）へ接続する際に頻繁に使用

## 関連項目

- [[JDBC]]
- [[SQL]]
- [[リレーショナルデータベース]]

## 参考

- [Open Database Connectivity - Wikipedia](https://en.wikipedia.org/wiki/Open_Database_Connectivity)
- [今さら学ぶ ODBC の基礎 - Qiita](https://qiita.com/mi-kana/items/1c851d7017b6a7fc3164)
- [ODBC の基礎 - Microsoft Learn](https://learn.microsoft.com/ja-jp/cpp/data/odbc/odbc-basics?view=msvc-170)
