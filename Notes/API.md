---
title: "API"
date: 2026-02-23
tags:
  - Web
  - Backend
  - API
related:
  - "[[RAG]]"
  - "[[MCP]]"
  - "[[Claude Code]]"
  - "[[Slidev]]"
  - "[[XaaS]]"
---

## 概要

API（Application Programming Interface）はソフトウェアコンポーネント間の通信規約。Web 開発では HTTP を使った Web API が主流であり、REST・GraphQL・gRPC・WebSocket が代表的なアーキテクチャスタイル。用途に応じて適切なスタイルを選択することが重要。

## 詳細

### 主要な Web API スタイル

#### REST（Representational State Transfer）

Roy Fielding が 2000 年の博士論文で提唱したアーキテクチャスタイル。HTTP のメソッドとリソース中心の URL で設計する。現在も最も普及している。

**6つの設計原則:**
1. ステートレス - サーバーはクライアントの状態を保持しない
2. クライアント・サーバー分離
3. キャッシュ可能性 - GET レスポンスはキャッシュできる
4. 階層型システム
5. 統一インターフェース - リソースを URL で識別
6. コードオンデマンド（任意）

```http
# REST の例
GET    /users/123           # ユーザー取得
POST   /users               # ユーザー作成
PUT    /users/123           # ユーザー更新
DELETE /users/123           # ユーザー削除
GET    /users/123/orders    # ユーザーの注文一覧
```

**HTTP ステータスコード:** 200 OK / 201 Created / 404 Not Found / 500 Internal Server Error 等で状態を表現。

**適切な用途:** 公開 API、シンプルな CRUD 操作、ブラウザからの直接アクセス

#### GraphQL

Facebook（Meta）が 2015 年に公開した API クエリ言語＋ランタイム。クライアントが必要なデータの形を指定でき、オーバーフェッチ（不要データの取得）・アンダーフェッチ（複数リクエスト必要）問題を解決する。

```graphql
# GraphQL の例：一度のリクエストで複数リソース取得
query {
  user(id: "123") {
    name
    email
    orders(last: 5) {
      id
      total
      status
    }
  }
}
```

**特徴:**
- 単一エンドポイント（`/graphql`）
- クライアントが取得フィールドを指定
- スキーマによる型定義
- エラーがあっても HTTP 200 を返す（レスポンス内の `errors` フィールドでエラーを報告）

**適切な用途:** モバイルアプリ（帯域節約）、複雑なデータグラフ、マイクロサービスの集約 API

#### gRPC

Google が開発した高性能 RPC（Remote Procedure Call）フレームワーク。Protocol Buffers でデータをバイナリシリアライズし、HTTP/2 上で通信する。

```proto
// Protocol Buffers でインターフェース定義
service UserService {
  rpc GetUser (UserRequest) returns (UserResponse);
  rpc ListUsers (ListRequest) returns (stream UserResponse);
}

message UserRequest {
  string user_id = 1;
}
```

**特徴:**
- バイナリシリアライズ（JSON より高速・軽量）
- HTTP/2 による多重化・サーバープッシュ
- 厳密な型定義（proto ファイル）
- ストリーミング対応（単項・サーバーサイド・クライアントサイド・双方向）
- ブラウザからは直接呼び出せない（grpc-web が必要）

**適切な用途:** マイクロサービス間通信、高スループット要件、多言語サービス間通信

#### WebSocket

全二重・双方向の持続的通信チャンネル。HTTP のハンドシェイク後、WebSocket プロトコルにアップグレードして常時接続を維持する。

```javascript
// WebSocket の例
const ws = new WebSocket('wss://api.example.com/live');

ws.onmessage = (event) => {
  console.log('受信:', event.data);
};

ws.send('こんにちは');  // サーバーへ送信
```

**適切な用途:** リアルタイムチャット、ライブスコア・株価、オンラインゲーム、コラボレーションツール

### スタイル比較

| 観点 | REST | GraphQL | gRPC | WebSocket |
|------|------|---------|------|-----------|
| データ形式 | JSON/XML | JSON | Protocol Buffers | バイナリ/テキスト |
| 通信プロトコル | HTTP/1.1〜2 | HTTP/1.1〜2 | HTTP/2 | WebSocket |
| キャッシュ | 容易 | 複雑 | 難しい | - |
| ブラウザ対応 | ◎ | ◎ | △（grpc-web）| ◎ |
| リアルタイム | ✗ | サブスクリプション | ストリーミング | ◎ |
| 型安全性 | なし | スキーマあり | proto ファイル | なし |

### API 設計のベストプラクティス

#### REST API のベストプラクティス

```
# 良い例
GET /users                # 複数形のリソース名
GET /users/{id}/orders    # ネストしたリソース
POST /users               # 動詞ではなく名詞

# 悪い例
GET /getUser              # 動詞を使用
GET /user_orders          # 一貫性のない命名
```

- バージョニング: `/api/v1/users`
- ページネーション: `?limit=20&offset=40` または カーソルベース
- HATEOAS: レスポンスに関連リソースへのリンクを含める

#### セキュリティ

- 認証: Bearer トークン（JWT）、API キー、OAuth 2.0
- HTTPS 必須
- レートリミット・スロットリング
- 入力バリデーション
- CORS 設定

### API に関連する概念

#### Webhook

「逆向きの API」。イベント発生時にサーバーから指定した URL にプッシュ通知する仕組み。GitHub の PR マージ通知、Stripe の決済完了通知などが代表例。

#### OpenAPI（Swagger）

REST API の仕様を YAML/JSON で記述する標準フォーマット。ドキュメント自動生成・クライアントコード生成に活用される。

```yaml
openapi: 3.0.0
paths:
  /users/{id}:
    get:
      summary: Get user by ID
      parameters:
        - name: id
          in: path
          required: true
          schema:
            type: string
```

#### [[MCP]]（Model Context Protocol）

AI モデルとツールを接続するためのプロトコル。API の一形態として、LLM が外部ツール・データソースと通信するための規約を提供する。

## ポイント

- REST は最も汎用的で学習コストが低い。公開 API のデファクト
- GraphQL はクライアントの柔軟なデータ取得が必要なとき（オーバーフェッチ問題を解決）
- gRPC はサービス間通信の高性能化が求められる内部 API に最適
- WebSocket はリアルタイム双方向通信が必要なアプリケーションに使用
- 実際には REST + WebSocket（リアルタイム部分のみ）など複数を組み合わせる設計が多い

## 関連項目

- [[RAG]] - 外部データソースへの API アクセスを活用する AI パターン
- [[MCP]] - AI モデルとツールを接続するプロトコル（API の一形態）
- [[Claude Code]] - Anthropic API を通じて Claude を利用するツール
- [[Slidev]] - Vite プラグイン API で拡張可能なプレゼンツール
- [[XaaS]] - クラウドサービスの多くは API 経由で利用する

## 参考

- [AWS: GraphQL vs REST の違い](https://aws.amazon.com/compare/the-difference-between-graphql-and-rest/)
- [Postman: Types of APIs](https://blog.postman.com/different-types-of-apis/)
- [GraphQL 公式サイト](https://graphql.org/)
- [gRPC 公式サイト](https://grpc.io/)
- [REST vs. GraphQL vs. gRPC vs. WebSocket - Resolute Software](https://www.resolutesoftware.com/blog/rest-vs-graphql-vs-grpc-vs-websocket/)
