---
title: "OLTP"
date: 2026-04-18
tags:
  - DB
  - SQL
related:
  - "[[OLAP]]"
  - "[[SQL]]"
  - "[[PostgreSQL]]"
  - "[[NoSQL]]"
---

## 概要

OLTP（Online Transaction Processing）は、業務上のトランザクションをリアルタイムに処理するデータ処理方式である。ECサイトの注文処理・銀行振込・在庫管理など、日常業務の中で大量かつ短時間に発生するデータの読み書きを確実に処理することを目的とする。データの整合性を保証する ACID 特性が重視される。

## 詳細

### ACID 特性

OLTP システムの中核をなす**ACID** 原則:

| 特性 | 英語 | 説明 |
|------|------|------|
| **原子性** | Atomicity | トランザクションは全て完了するか全て失敗するかのいずれか |
| **一貫性** | Consistency | トランザクション前後でデータの整合性制約が維持される |
| **分離性** | Isolation | 並行するトランザクションが互いに干渉しない |
| **永続性** | Durability | コミット済みのデータはシステム障害後も保持される |

### [[OLAP]] との比較

| 観点 | OLTP | [[OLAP]] |
|------|------|------|
| 目的 | 業務処理・トランザクション | 分析・集計 |
| クエリ | シンプル、少数レコード | 複雑な集計、大量レコード |
| 操作 | 書き込み中心 | 読み取り中心 |
| DB 構造 | 行指向（ロー指向） | 列指向（カラムナー） |
| スキーマ | 正規化（冗長性を排除） | 非正規化（スタースキーマ） |
| レスポンス | ミリ秒単位 | 秒〜分単位 |
| データ量 | 比較的小〜中規模 | 数億〜数兆行規模 |

### 行指向ストレージ

OLTP の性能の鍵は**行指向（Row-Oriented）ストレージ**:

```
行指向（OLTP向き）:
[id=1, name=Alice, age=30] [id=2, name=Bob, age=25] ...
→ 1行全体を1回のI/Oで読み書きできる
→ INSERT/UPDATE/DELETE が効率的
```

- 1トランザクションでは通常 1〜数行のデータを扱う
- 行ごとにまとめてディスクに書き込むため、個別レコードの取得・更新が高速

### 同時実行制御

複数ユーザーが同時に同じデータを操作する場合の制御:

- **ロック機構**: 悲観的ロック（Pessimistic）または楽観的ロック（Optimistic）
- **MVCC**（Multi-Version Concurrency Control）: PostgreSQL・MySQL が採用。読み取りと書き込みをブロックしない
- 二重予約防止（航空券予約など）、残高不整合防止（銀行）などを実現

### データ正規化

OLTP では冗長性を排除し更新異常を防ぐため、第3正規形（3NF）などに正規化されたスキーマを採用:

- テーブルを適切に分割し、外部キーで結合
- 更新コストを最小化、データ整合性を保証
- 複雑な集計クエリには向かない（→ [[OLAP]] システムに移送して分析）

### 代表的な OLTP データベース

| DB | 特徴 |
|----|------|
| **[[PostgreSQL]]** | OSS RDBMS。MVCC・ACID 完全対応。拡張性が高い |
| **MySQL** | 最も広く使われる OSS RDBMS |
| **Oracle Database** | エンタープライズ向け商用 RDBMS |
| **SQL Server** | Microsoft の商用 RDBMS |
| **Amazon RDS / Aurora** | AWS マネージド OLTP DB |
| **Google Cloud Spanner** | グローバル分散 OLTP（[[NewSQL]]） |

### データフロー全体像

```
ユーザー操作
    ↓
OLTP システム（業務 DB）
    ↓ ETL / CDC（Change Data Capture）
データウェアハウス（DWH）
    ↓
OLAP システム（分析 DB）
    ↓
BI ツール → 意思決定
```

### ユースケース

- **金融**: 銀行口座の入出金、証券取引
- **EC**: 注文処理、在庫引き当て、決済
- **航空・交通**: 座席予約、運賃計算
- **製造**: 受発注管理、在庫管理

## ポイント

- OLTP は ACID 保証と行指向ストレージを軸に、小さいトランザクションの高速な読み書きに最適化
- [[OLAP]] とは目的が正反対のため、通常は別システムに分離する（ETL/CDC でデータ連携）
- MVCC により読み取りと書き込みをブロックせずに並行処理を実現（PostgreSQL など）
- データ量が増えると OLTP DB の集計クエリが重くなるため、[[データウェアハウス]] への分離が重要
- [[NoSQL]] の BASE（結果整合性）とは対比される概念。金融・在庫など整合性が必須な領域では OLTP + ACID が基本

## 関連項目

- [[OLAP]] - 分析処理との対比
- [[SQL]] - OLTP クエリの記述に使用
- [[PostgreSQL]] - 代表的な OSS OLTP データベース
- [[NoSQL]] - 結果整合性（BASE）を採用する非 OLTP 系 DB

## 参考

- [オンライン・トランザクション処理（OLTP）とは - Microsoft Azure](https://learn.microsoft.com/ja-jp/azure/architecture/data-guide/relational-data/online-transaction-processing)
- [What Is Online Transactional Processing (OLTP)? | IBM](https://www.ibm.com/think/topics/oltp)
- [What Is Online Transaction Processing (OLTP)? | Oracle](https://www.oracle.com/database/what-is-oltp/)
- [OLTP vs OLAP in 2026 | ClickHouse](https://clickhouse.com/resources/engineering/oltp-vs-olap)
- [OLTPとOLAPの違い | GiXo](https://www.gixo.jp/blog/2934/)
