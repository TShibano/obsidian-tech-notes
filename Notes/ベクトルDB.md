---
title: "ベクトルDB"
date: 2026-04-18
tags:
  - DB
  - AI
  - LLM
related:
  - "[[RAG]]"
  - "[[NoSQL]]"
  - "[[SurrealDB]]"
  - "[[PostgreSQL]]"
---

## 概要

ベクトルDB（ベクトルデータベース）は、機械学習モデルが生成した高次元のベクトル埋め込み（embedding）を格納・検索するために特化したデータベースである。意味的類似性に基づく近似最近傍探索（ANN）を高速に実行できるため、[[RAG]] システム・セマンティック検索・レコメンデーションなど AI アプリケーションの基盤として広く使われる。

## 詳細

### 仕組み

#### ベクトル埋め込みとは

テキスト・画像・音声などのデータを、意味的関係を保持した数値ベクトル（浮動小数点数の配列）に変換したもの。例えば「猫」と「子猫」は意味的に近いため、ベクトル空間でも近い位置にマッピングされる。

```
テキスト → 埋め込みモデル → [0.12, -0.34, 0.87, ..., 0.05]（例：1536次元）
```

#### 類似度検索

クエリをベクトル化し、データベース内の全ベクトルとの類似度を計算する。類似度の指標:

| 指標 | 用途 |
|------|------|
| コサイン類似度 | テキスト・文書の意味的類似性 |
| ユークリッド距離 | 空間的な距離 |
| 内積（Dot Product） | 正規化済みベクトルではコサインと同等 |

#### インデックスアルゴリズム（ANN）

全件走査（線形スキャン）ではなく近似最近傍探索（ANN）で高速化:

| アルゴリズム | 概要 |
|-------------|------|
| **HNSW**（Hierarchical Navigable Small World） | グラフベース。精度と速度のバランスが良く最もよく使われる |
| **IVF**（Inverted File Index） | クラスタリングベース。大規模データに有効 |
| **PQ**（Product Quantization） | 量子化によるメモリ効率化 |
| **LSH**（Locality-Sensitive Hashing） | ハッシュベースの近似探索 |

### 主要なベクトルDB

#### 専用型（フルマネージド）

| DB | 特徴 |
|----|------|
| **Pinecone** | フルマネージド SaaS。PoC 最速。Starter プランで 2GB まで無料 |
| **OpenAI Vector Store** | OpenAI API と統合したフルマネージド型 |

#### 専用型（OSS）

| DB | 特徴 |
|----|------|
| **Milvus / Zilliz Cloud** | 大規模（数億件規模）対応。インデックスのチューニングが細かく可能 |
| **Qdrant** | Rust 製で高速。メタデータフィルタリングが柔軟。軽量で起動が速い |
| **Weaviate** | モジュール構成。BM25 + ベクトルのネイティブハイブリッド検索対応 |
| **Chroma** | Python ファースト。小規模・ローカル開発向き |

#### 拡張型（既存 DB に追加）

| DB | 特徴 |
|----|------|
| **pgvector** | [[PostgreSQL]] の拡張。HNSW 対応。数十万〜数百万件規模で実用的 |
| **Redis（RediSearch）** | インメモリ型。低レイテンシ優先 |
| **Elasticsearch / OpenSearch** | 全文検索との組み合わせに強い |
| **SurrealDB** | [[SurrealDB]] はマルチモデルでベクトル検索を内蔵 |

### 選定ガイド

| シナリオ | 推奨 |
|---------|------|
| PoC・最速スタート | Pinecone（フルマネージド） |
| 数億件規模 + チューニング | Milvus（OSS 自社運用） |
| 既存 PostgreSQL インフラ活用 | pgvector |
| リアルタイム性・低レイテンシ優先 | Qdrant |
| BM25 + ベクトルのハイブリッド検索 | Weaviate |

### RAG との関係

[[RAG]]（Retrieval-Augmented Generation）において、ベクトルDBはナレッジベースの検索エンジンとして機能する:

1. ドキュメントをチャンク分割 → 埋め込みモデルでベクトル化 → ベクトルDB に保存
2. ユーザーのクエリをベクトル化 → ベクトルDB で類似チャンクを検索
3. 検索結果を LLM に文脈として渡して回答生成

## ポイント

- 通常のキーワード検索では見つけられない「意味的に近い」データを検索できる点が最大の特徴
- HNSW インデックスが現在のデファクトスタンダード。精度と速度のトレードオフを管理できる
- 専用型・拡張型・フルマネージド型でユースケースが異なる。スケール要件と運用コストで選定
- pgvector は数百万件以下であれば既存 [[PostgreSQL]] インフラで十分対応可能
- LLM の長期記憶・エージェントの記憶管理（メモリ）としても活用が進む

## 関連項目

- [[RAG]] - ベクトルDB の代表的なユースケース
- [[NoSQL]] - ベクトルDB は NoSQL の一種として分類されることも多い
- [[SurrealDB]] - ベクトル検索を内蔵したマルチモデル DB
- [[PostgreSQL]] - pgvector 拡張でベクトル検索が可能

## 参考

- [What is a Vector Database and How Does it Work? | NVIDIA](https://www.nvidia.com/en-us/glossary/vector-database/)
- [ベクトルデータベースとは | Elastic](https://www.elastic.co/jp/what-is/vector-database)
- [What Is a Vector Database? | IBM](https://www.ibm.com/think/topics/vector-database)
- [What is a vector database? | Cloudflare](https://www.cloudflare.com/learning/ai/what-is-vector-database/)
- [ベクトルデータベース比較【2026年版】](https://plus.cmknet.co.jp/vector-database/)
