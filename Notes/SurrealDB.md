---
title: "SurrealDB"
date: 2026-04-18
tags:
  - DB
  - NoSQL
related:
  - "[[NoSQL]]"
  - "[[NewSQL]]"
  - "[[ベクトルDB]]"
  - "[[RAG]]"
---

## 概要

SurrealDB は Rust 製のマルチモデルデータベースで、ドキュメント・グラフ・リレーショナル・時系列・地理空間・キーバリューの各データモデルを1つのエンジンに統合する。独自クエリ言語 **SurrealQL**（SQL・NoSQL・グラフの概念を融合）を持ち、ベクトル検索・全文検索・リアルタイム購読に対応する。2026年2月には AI エージェントのコンテキスト管理に特化した SurrealDB 3.0 が GA となり $23M の資金調達を実施した。

## 詳細

### アーキテクチャ

単一の Rust バイナリとして配布され、以下の形態で動作する:

| 動作モード | 説明 |
|------------|------|
| 組み込み（in-app） | アプリケーション内に埋め込んでライブラリとして利用 |
| ブラウザ（WASM） | WebAssembly 経由でブラウザ上で動作 |
| エッジ | エッジコンピューティング環境で動作 |
| シングルノード | セルフホストのバックエンドサーバー |
| 分散クラスター | クラウド上での分散構成 |

### SurrealQL

SurrealQL は SQL に近い構文を持ちながら、グラフ走査・ドキュメント操作・スキーマレスな柔軟性を兼ね備えたクエリ言語:

- `SELECT`, `INSERT`, `UPDATE`, `DELETE` など SQL ライクな構文
- `->` 演算子でグラフリレーションを辿るグラフクエリ
- ネストされたドキュメント構造への直接アクセス
- REST・WebSocket（JSON-RPC）・GraphQL（β）での利用が可能

### データモデルと検索機能

| 機能 | 内容 |
|------|------|
| ドキュメント | JSON ベースのフレキシブルなスキーマ |
| グラフ | ノード・エッジで関係を表現。コンテキストグラフとして AI エージェントのメモリに活用 |
| 時系列 | センサー・ログデータ向け |
| ベクトル検索 | 埋め込みベクトルによる類似度検索（ANN） |
| 全文検索 | BM25 ベースの全文検索インデックス |
| ハイブリッド検索 | ベクトル + キーワード検索の組み合わせ |

### SurrealDB 3.0（2026）

2026年2月リリース。AI エージェントのコンテキスト管理に特化した改善が中心:

- **オンディスクドキュメント表現の再設計**: 安定性・パフォーマンス向上
- **実行可能式と値の分離**: データと処理ロジックの明確な区別
- **ID ベースのメタデータストレージ**: 効率的なメタデータ管理
- **コンテキストグラフ**: エージェントの記憶・状態をグラフ構造でDB内に直接保持
- **デフォルトで同期書き込み**: データ整合性の向上

### [[NoSQL]] との位置付け

SurrealDB は複数の NoSQL カテゴリを単一のエンジンで扱うため、「マルチモデル NoSQL」と分類される。[[NewSQL]] のような ACID 保証も提供しており、境界は曖昧になっている。

## ポイント

- マルチモデル（ドキュメント・グラフ・時系列・KV など）を単一 DB で扱えるため、ポリグロット・パーシステンスを1つのシステムで解決できる
- Rust 製の単一バイナリで、組み込みからクラウドクラスターまで同一 DB を使い回せるのが強み
- SurrealDB 3.0 から AI エージェントのコンテキスト・メモリ管理に特化した機能が拡充され、「エージェント向けコンテキスト層」として位置付けが明確化
- [[RAG]] システムのベクトルストアとしても利用可能（ベクトル検索＋グラフ構造の組み合わせ）
- スキーマレスから厳格なスキーマまで段階的に定義できる柔軟性を持つ

## 関連項目

- [[NoSQL]] - SurrealDB が属するデータベースカテゴリ
- [[NewSQL]] - ACID + 水平スケールを目指す新世代 DB との比較
- [[ベクトルDB]] - SurrealDB はベクトル検索機能を内蔵
- [[RAG]] - Retrieval-Augmented Generation。SurrealDB をベクトルストアとして利用可能

## 参考

- [SurrealDB 公式サイト](https://surrealdb.com/)
- [What is SurrealDB | SurrealDB Docs](https://surrealdb.com/docs/surrealdb)
- [SurrealDB Features](https://surrealdb.com/features)
- [SurrealDB raises $23M, launches SurrealDB 3.0 - TechTarget](https://www.techtarget.com/searchdatamanagement/news/366639042/SurrealDB-raises-23M-launches-update-to-fuel-agentic-AI)
- [SurrealDB secures $23M and launches SurrealDB 3.0 - Tech.eu](https://tech.eu/2026/02/17/surrealdb-secures-23m-and-launches-surrealdb-3-0-to-address-ai-agent-memory-challenges/)
- [GitHub - surrealdb/surrealdb](https://github.com/surrealdb/surrealdb)
