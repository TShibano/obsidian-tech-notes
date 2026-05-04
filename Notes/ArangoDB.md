---
title: "ArangoDB"
date: 2026-05-05
tags:
  - DB
  - NoSQL
related:
  - "[[NoSQL]]"
  - "[[ポリグロットデータストア]]"
  - "[[ドキュメントDB]]"
  - "[[KVストア]]"
  - "[[ポリグロット永続化]]"
---

## 概要

ArangoDB は，ドキュメント・グラフ・キーバリューの3つのデータモデルを単一エンジンでネイティブにサポートするオープンソースのマルチモデルデータベースである．AQL（ArangoDB Query Language）という統一クエリ言語で全データモデルを横断的に操作できる．

## 詳細

### マルチモデルの特徴

| サポートモデル | 説明 |
|-------------|------|
| **ドキュメント** | JSON ドキュメントを柔軟なスキーマで格納 |
| **グラフ** | ノードとエッジをドキュメントとして管理．多段関係のトラバースが可能 |
| **キーバリュー** | キーで高速アクセス（ドキュメントモデルの特殊ケース） |

複数モデルを1エンジンで扱えるため，[[ポリグロット永続化]] でありがちな「複数 DB の運用管理コスト」を削減できる．

### AQL（ArangoDB Query Language）

SQL に似た構文で，ドキュメント検索・集計・JOIN・グラフトラバース（深さ優先・幅優先）を統一言語で記述できる：

```aql
// ドキュメント検索
FOR u IN users
  FILTER u.age > 25
  RETURN u

// グラフトラバース（1〜3ホップ）
FOR v, e, p IN 1..3 OUTBOUND 'users/alice' GRAPH 'socialGraph'
  RETURN { vertex: v, edge: e }

// ドキュメントとグラフの横断クエリ
FOR user IN users
  FOR friend IN 1..1 OUTBOUND user GRAPH 'friends'
    FILTER friend.country == 'JP'
    RETURN { user: user.name, friend: friend.name }
```

### 主な機能

| 機能 | 説明 |
|------|------|
| **ArangoSearch** | 全文検索・スコアリング・ファセット．Lucene ベースのインデックス |
| **Pregel** | 分散グラフ処理フレームワーク（PageRank 等の大規模グラフ分析） |
| **Foxx** | サーバーサイド JavaScript によるマイクロサービス機能 |
| **スマートグラフ** | グラフデータのシャーディングを最適化する独自機能 |
| **クラスタ** | シャーディング + レプリケーションで水平スケール |

### Cosmos DB / SurrealDB との比較

| 製品 | 特徴 | 主な用途 |
|------|------|---------|
| **ArangoDB** | OSS，統一 AQL，グラフ特化機能 | グラフ×ドキュメントの複合ユースケース |
| **Azure Cosmos DB** | フルマネージド，5つの整合性レベル，グローバル分散 | エンタープライズ・マルチリージョン |
| **[[SurrealDB]]** | Rust 製，マルチモデル，組み込み対応 | 新興．エッジ・マルチモデル |

### ユースケース

- **ナレッジグラフ**: エンティティとその関係性をドキュメント+グラフで表現
- **推薦エンジン**: ユーザー×商品の関係グラフを AQL でトラバース
- **不正検知**: 取引ネットワークのパターン分析
- **マスターデータ管理**: 異なるデータ構造のマスターを1DBに統合

## ポイント

- 「1つの DB で複数モデル」を実現することで，[[ポリグロット永続化]] の運用コストを低減
- AQL は SQL 経験者が習得しやすく，グラフトラバースも同じ言語で記述できる
- グラフ機能の深さは Neo4j には及ばないが，ドキュメント機能との組み合わせが強み
- オープンソース（Apache 2.0）で，ArangoGraph（クラウド版）も提供
- 日本語ドキュメントは限られるが，英語ドキュメントは充実

## 関連項目

- [[NoSQL]] — ArangoDB が属するデータベースカテゴリ
- [[ポリグロットデータストア]] — ArangoDB は複数モデルを統合するマルチモデル DB
- [[ドキュメントDB]] — ArangoDB が対応するデータモデルの一つ
- [[KVストア]] — ArangoDB が対応するデータモデルの一つ
- [[ポリグロット永続化]] — 複数モデルを1DBで扱うことで運用コストを削減

## 参考

- [ArangoDB GitHub](https://github.com/arangodb/arangodb)
- [ArangoDB - Wikipedia](https://en.wikipedia.org/wiki/ArangoDB)
- [マルチモデルデータベース「ArangoDB」に要注目 | TechTarget](https://techtarget.itmedia.co.jp/tt/news/1903/19/news02.html)
- [ArangoDBとは — マルチモデルDBの特徴・AQL・ArangoSearchから導入・クラスタ運用まで | EverPlay](https://everplay.jp/column/18899)
