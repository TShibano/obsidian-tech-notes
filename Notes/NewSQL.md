---
title: "NewSQL"
date: 2026-02-14
tags:
  - DB
  - SQL
related:
  - "[[SQL]]"
  - "[[PostgreSQL]]"
  - "[[データ基盤]]"
  - "[[NoSQL]]"
---

## 概要

NewSQL は、従来の RDBMS が持つ ACID トランザクション保証と [[SQL]] 互換性を維持しつつ、NoSQL が得意とする水平スケーラビリティと高可用性を両立させる、新世代の分散リレーショナルデータベースである。2011年に 451 Group のアナリスト Matthew Aslett が命名した。

## 詳細

### 背景と歴史

従来の RDBMS（[[PostgreSQL]]、MySQL、Oracle 等）は ACID 特性に優れるが、垂直スケーリングが中心であり、大規模分散環境への対応が困難であった。一方、2000年代後半に台頭した NoSQL（MongoDB、Cassandra 等）は水平スケーリングに優れるが、トランザクションの一貫性を犠牲にする BASE（Basically Available, Soft state, Eventual consistency）モデルを採用していた。

NewSQL は「ACID もスケーラビリティも諦めない」という発想から生まれた。最初期のシステムとしては H-Store（MIT）があり、その後 Google Spanner（2012年論文公開）が分散 SQL の可能性を実証した。

| 年 | 出来事 |
|----|--------|
| **2007年** | H-Store プロジェクト（MIT）開始 |
| **2011年** | Matthew Aslett が「NewSQL」という用語を提唱 |
| **2012年** | Google Spanner の論文公開 |
| **2014年** | CockroachDB のプロジェクト開始 |
| **2015年** | PingCAP が TiDB を開発開始 |
| **2016年** | YugabyteDB のプロジェクト開始 |
| **2017年** | Google Cloud Spanner が一般提供開始 |

### SQL・NoSQL・NewSQL の比較

| 観点 | 従来の SQL（RDBMS） | NoSQL | NewSQL |
|------|---------------------|-------|--------|
| **データモデル** | リレーショナル（テーブル） | ドキュメント、KV、グラフ等 | リレーショナル（テーブル） |
| **クエリ言語** | SQL | 独自 API | SQL |
| **ACID** | 完全保証 | 一部（結果整合性が多い） | 完全保証 |
| **スケーリング** | 垂直（スケールアップ） | 水平（スケールアウト） | 水平（スケールアウト） |
| **一貫性モデル** | 強い一貫性 | 結果整合性（BASE） | 強い一貫性 |
| **可用性** | フェイルオーバー構成 | 組み込み高可用性 | 組み込み高可用性 |
| **スキーマ** | 固定スキーマ | スキーマレス/柔軟 | 固定スキーマ |

### アーキテクチャの特徴

NewSQL データベースに共通する主要なアーキテクチャ上の特徴:

- **分散アーキテクチャ**: データを複数ノードに自動的にシャーディング・レプリケーションし、水平スケーリングを実現
- **Shared-Nothing 構成**: ノード間でストレージを共有せず、単一障害点を排除
- **コンセンサスプロトコル**: Raft や Paxos を使い、分散ノード間でデータの一貫性を保証
- **分散トランザクション**: 複数ノードにまたがるトランザクションを ACID で実行（2フェーズコミット等）
- **自動リバランス**: ノード追加・削除時にデータを自動的に再配置

### 主要な NewSQL データベース

#### Google Cloud Spanner

Google が開発した世界初のグローバル分散 SQL データベース。TrueTime API（原子時計・GPS 時計による精密な時刻同期）を使い、分散環境で外部整合性（External Consistency）を実現する。

- **時刻同期**: TrueTime API により上限 7ms の精度でノード間時刻を同期
- **一貫性**: 直列化可能性よりも強い「外部整合性」を保証
- **制限**: Google Cloud Platform でのみ動作（ベンダーロックイン）

#### CockroachDB

Google Spanner にインスパイアされたオープンソース NewSQL データベース。原子時計なしでの分散 SQL を実現する。

- **SQL 互換**: [[PostgreSQL]] ワイヤプロトコル互換
- **コンセンサス**: Raft プロトコルを採用（Spanner は Paxos）
- **一貫性**: 直列化可能性（Serializable）を保証（Spanner の外部整合性よりは弱い）
- **クラウド非依存**: マルチクラウド、オンプレミス、ハイブリッド構成が可能
- **ライセンス**: BSL（Business Source License）— 完全なオープンソースではない

#### TiDB

PingCAP が開発する MySQL 互換の NewSQL データベース。HTAP（Hybrid Transactional/Analytical Processing）を特徴とする。

- **SQL 互換**: MySQL プロトコル互換
- **ストレージ**: TiKV（行ストレージ）＋ TiFlash（列ストレージ）による HTAP
- **HTAP**: トランザクション処理と分析処理を単一システムで実行可能
- **ライセンス**: Apache 2.0（完全オープンソース）

#### YugabyteDB

[[PostgreSQL]] 互換の分散 SQL データベース。PostgreSQL のクエリレイヤーコードを再利用し、高い互換性を実現する。

- **SQL 互換**: PostgreSQL 互換性スコア 85.08%（CockroachDB の 53.66% を大幅に上回る）
- **アーキテクチャ**: YQL（クエリレイヤー）＋ DocDB（分散ストレージ）
- **ライセンス**: Apache 2.0（完全オープンソース）

#### 主要 NewSQL 比較表

| 観点 | Spanner | CockroachDB | TiDB | YugabyteDB |
|------|---------|-------------|------|------------|
| **SQL 互換** | PostgreSQL 風 | PostgreSQL | MySQL | PostgreSQL |
| **HTAP** | 限定的 | 限定的 | あり（TiFlash） | 限定的 |
| **クラウド依存** | GCP のみ | 非依存 | 非依存 | 非依存 |
| **OSS** | 非公開 | BSL | Apache 2.0 | Apache 2.0 |
| **コンセンサス** | Paxos | Raft | Raft | Raft |
| **時刻同期** | TrueTime（原子時計） | HLC（論理時計） | TSO（中央発行） | HLC |

### ユースケース

NewSQL が特に適しているユースケース:

- **金融サービス**: 高頻度取引、決済処理（厳密な一貫性とスケーラビリティが必須）
- **EC プラットフォーム**: 在庫管理、注文処理（グローバル展開 × ACID トランザクション）
- **通信業界**: 課金・加入者管理
- **ゲーム**: リーダーボード、プレイヤーデータ管理（リアルタイム × 大規模）
- **グローバルアプリケーション**: マルチリージョン展開が必要なシステム

### 選定指針

| 要件 | 推奨 |
|------|------|
| 従来の OLTP（中小規模） | [[PostgreSQL]]、MySQL |
| 水平スケール＋ACID が必須 | CockroachDB、YugabyteDB |
| MySQL エコシステムからの移行 | TiDB |
| PostgreSQL 互換重視 | YugabyteDB |
| HTAP（OLTP＋OLAP 統合） | TiDB |
| GCP ネイティブ＋最高の一貫性 | Google Cloud Spanner |

### 2025〜2026年のトレンド

- **クラウドネイティブ化**: CockroachDB、YugabyteDB 等がマルチクラウド・マルチリージョン対応を強化
- **HTAP の進化**: TiDB の TiFlash に代表される、トランザクションと分析の融合が進行
- **AI 統合**: クエリ最適化やデータベース管理の AI 自動化が各製品で進行中
- **PostgreSQL 互換競争**: CockroachDB と YugabyteDB が [[PostgreSQL]] 互換性の向上を競う
- **業界再編**: PE ファンドによる SingleStore、Couchbase 等の買収が進み、統合が加速

## ポイント

- NewSQL は ACID + SQL 互換性 + 水平スケーラビリティを同時に実現する分散 RDBMS
- Google Spanner が原子時計を使い先駆的に実証し、CockroachDB・TiDB・YugabyteDB が OSS で追随
- 従来の RDBMS の代替ではなく、大規模 × 強一貫性が必要なユースケースに適する
- PostgreSQL 互換（CockroachDB、YugabyteDB）と MySQL 互換（TiDB）に大別される
- 2025年以降は HTAP、AI 統合、マルチクラウド対応がトレンド

## 関連項目

- [[SQL]] - NewSQL の基盤となる標準クエリ言語
- [[PostgreSQL]] - CockroachDB・YugabyteDB が互換性を目指す RDBMS
- [[データ基盤]] - NewSQL を組み込むデータアーキテクチャ全体
- [[NoSQL]] - NewSQL が解決しようとした水平スケール型の非リレーショナル DB

## 参考

- [NewSQL - Wikipedia](https://en.wikipedia.org/wiki/NewSQL)
- [451 Research - Matthew Aslett's Blog](https://blogs.the451group.com/information_management/2011/04/06/what-we-talk-about-when-we-talk-about-newsql/)
- [Cockroach Labs - Spanner vs CockroachDB](https://www.cockroachlabs.com/blog/spanner-vs-cockroachdb/)
- [Cockroach Labs - Living without Atomic Clocks](https://www.cockroachlabs.com/blog/living-without-atomic-clocks/)
- [PingCAP - What is a Distributed Database?](https://www.pingcap.com/article/what-is-a-distributed-database-a-complete-guide/)
- [YugabyteDB vs CockroachDB](https://www.yugabyte.com/yugabytedb-vs-cockroachdb/)
- [Andy Pavlo - 2025 Databases Retrospective](https://www.cs.cmu.edu/~pavlo/blog/2026/01/2025-databases-retrospective.html)
- [Distributed SQL 2025: CockroachDB vs TiDB vs YugabyteDB](https://sanj.dev/post/distributed-sql-databases-comparison)
