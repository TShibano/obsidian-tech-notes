---
title: "JDBC"
date: 2026-05-10
tags:
  - DB
  - Language
related:
  - "[[ODBC]]"
  - "[[SQL]]"
  - "[[リレーショナルデータベース]]"
---

## 概要

JDBC（Java Database Connectivity）は，JavaアプリケーションからデータベースへアクセスするためのAPI（java.sql パッケージ）．Microsoftの[[ODBC]]をベースに1997年にSun Microsystemsが策定．JDBCドライバを切り替えるだけで異なるDBMSに接続でき，DB種別を抽象化する．

## 詳細

### アーキテクチャ

```
Java アプリケーション
      ↓ java.sql.* API
JDBC API（JDK 組み込み）
      ↓ DriverManager.getConnection()
JDBCドライバ（各DBMS提供，jar ファイル）
      ↓ DBMS ネイティブプロトコル
データベースサーバ
```

### JDBCドライバの種類

| タイプ | 説明 | 特徴 |
|--------|------|------|
| Type 1 | [[ODBC]] ブリッジ | ODBC ドライバを経由，非推奨 |
| Type 2 | ネイティブ API ブリッジ | DBMS の C/C++ ライブラリを使用 |
| Type 3 | ネットワークプロトコル | サーバサイドミドルウェア経由，ピュアJava |
| Type 4 | ダイレクトネットワーク | DBMSプロトコルを直接実装，最も一般的 |

現代ではType 4（ピュアJavaドライバ）が主流．

### 主な操作フロー

```java
// 1. 接続
Connection conn = DriverManager.getConnection(url, user, password);

// 2. ステートメント準備
PreparedStatement stmt = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
stmt.setInt(1, userId);

// 3. クエリ実行
ResultSet rs = stmt.executeQuery();

// 4. 結果取得
while (rs.next()) {
    String name = rs.getString("name");
}

// 5. クローズ
rs.close(); stmt.close(); conn.close();
```

### [[ODBC]] との比較

| 比較項目 | JDBC | [[ODBC]] |
|---------|------|-------|
| 対象言語 | Java | C/C++（他言語はブリッジ） |
| プラットフォーム | JVM（クロスプラットフォーム） | OS依存 |
| ドライバ形式 | JAR ファイル | DLL / .so ファイル |
| 接続プール | DataSource / コネクションプール | OS管理 |
| トランザクション | `Connection.setAutoCommit(false)` | SQLSetConnectAttr |

### JPA / Hibernate との関係

JDBC はローレベル API であり，Spring Data JPA，Hibernate，MyBatis 等のO/Rマッパーはその上位レイヤー．これらのフレームワークも最終的には JDBC を通じてDBにアクセスする．

## ポイント

- PreparedStatement を使うことで SQL インジェクションを防止できる（パラメータのエスケープが自動）
- コネクションプール（HikariCP，c3p0）を使わないとDB接続のオーバーヘッドが大きくなる
- データエンジニアリング領域では Spark JDBC，dbt adapter，Airflow 接続等で間接的に多用される

## 関連項目

- [[ODBC]]
- [[SQL]]
- [[リレーショナルデータベース]]

## 参考

- [Java Database Connectivity - Wikipedia](https://en.wikipedia.org/wiki/Java_Database_Connectivity)
- [JDBC ドライバの基礎知識 - CData](https://jp.cdata.com/blog/what-is-jdbc-driver-in-java)
- [ODBC/JDBC とは - GiXo](https://www.gixo.jp/blog/12396/)
