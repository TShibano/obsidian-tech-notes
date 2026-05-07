---
title: "REST API"
date: 2026-05-07
tags:
  - Web
  - API
  - Backend
related:
  - "[[API]]"
  - "[[HTTP・HTTPS]]"
  - "[[GraphQL]]"
  - "[[RPC・gRPC]]"
  - "[[Webhook]]"
  - "[[冪等]]"
---

## 概要

REST（Representational State Transfer）は，Roy Fielding が 2000 年の博士論文で提唱した Web API の設計スタイル．HTTP プロトコルを基盤に，リソース指向の統一インタフェースでクライアント・サーバ間の通信を定義する．

## 詳細

### 6 つのアーキテクチャ制約

| 制約 | 説明 |
|------|------|
| **統一インタフェース** | リソースを URI で識別し，HTTP メソッド（GET / POST / PUT / DELETE / PATCH）で操作する |
| **クライアント・サーバ分離** | UI とデータストレージを分離し，双方が独立して進化できる |
| **ステートレス** | セッション状態はクライアントが保持し，各リクエストは独立して解釈可能 |
| **キャッシュ可能** | レスポンスにキャッシュ可否を明示し，中間層でのキャッシュを許可 |
| **レイヤードシステム** | クライアントは直接サーバと通信しているか分からなくてよい（プロキシ，CDN 等） |
| **コードオンデマンド（省略可）** | サーバがクライアントに実行可能コードを送信できる（JavaScript 等） |

### HTTP メソッドと CRUD の対応

| HTTP メソッド | CRUD 操作 | 冪等 |
|--------------|---------|------|
| GET | Read | YES |
| POST | Create | NO |
| PUT | Update（全体置換） | YES |
| PATCH | Update（部分更新） | NO |
| DELETE | Delete | YES |

### RESTful 設計のポイント

- URI はリソース（名詞）を表し，動詞は使わない（`/users/123` ○，`/getUser` ×）
- ステータスコードで結果を伝える（200, 201, 400, 404, 500 等）
- レスポンス形式は主に JSON

## ポイント

- 現在の Web API の事実上の標準スタイル
- ステートレスのため水平スケールが容易
- [[GraphQL]] はオーバー・アンダーフェッチ問題に対する代替手段
- [[RPC・gRPC]] はマイクロサービス間通信で高速だが HTTP/2 前提

## 関連項目

- [[API]]
- [[HTTP・HTTPS]]
- [[GraphQL]]
- [[RPC・gRPC]]
- [[Webhook]]
- [[冪等]]

## 参考

- [REST - Wikipedia](https://en.wikipedia.org/wiki/Representational_state_transfer)
- [What is RESTful API? - AWS](https://aws.amazon.com/what-is/restful-api/)
- [REST Architectural Constraints - REST API Tutorial](https://restfulapi.net/rest-architectural-constraints/)
