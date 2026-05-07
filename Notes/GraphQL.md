---
title: "GraphQL"
date: 2026-05-07
tags:
  - Web
  - API
  - Backend
related:
  - "[[API]]"
  - "[[REST API]]"
  - "[[HTTP・HTTPS]]"
  - "[[JSON]]"
---

## 概要

GraphQL は，Facebook（現 Meta）が 2012 年に開発し 2015 年に公開した API クエリ言語および実行エンジン．クライアントが必要なデータの形を宣言的に指定できるため，オーバーフェッチ・アンダーフェッチを解消する．

## 詳細

### 主要コンセプト

| 概念 | 説明 |
|------|------|
| **スキーマ** | 型定義言語（SDL）で API の構造を厳密に定義する |
| **クエリ（Query）** | データの読み取り操作 |
| **ミューテーション（Mutation）** | データの書き込み・更新・削除操作 |
| **サブスクリプション（Subscription）** | WebSocket 経由のリアルタイム更新 |
| **リゾルバ** | 各フィールドの値をどう取得するかを定義する関数 |

### REST との比較

| 観点 | REST | GraphQL |
|------|------|---------|
| エンドポイント | リソースごとに複数 | 1 つ（/graphql） |
| データ取得 | サーバ定義の固定レスポンス | クライアントが形を指定 |
| オーバーフェッチ | 起きやすい | 起きない |
| アンダーフェッチ | 起きやすい | 起きない |
| キャッシュ | HTTP キャッシュが使いやすい | パーサス（Persisted Queries 等が必要） |
| 学習コスト | 低 | 中 |

### イントロスペクション

スキーマを実行時に問い合わせて型情報を取得できる機能．IDE 補完やドキュメント自動生成に活用される．

## ポイント

- BFF（Backend for Frontend）パターンと相性が良い
- 強い型システムにより API の整合性を保証
- N+1 問題が発生しやすいため DataLoader 等のバッチロードが必要
- 単一エンドポイントのため WAF / キャッシュの設計が REST と異なる

## 関連項目

- [[API]]
- [[REST API]]
- [[HTTP・HTTPS]]
- [[JSON]]

## 参考

- [GraphQL 公式サイト](https://graphql.org/)
- [GraphQL - Wikipedia](https://en.wikipedia.org/wiki/GraphQL)
- [GraphQL Specification](https://spec.graphql.org/)
