---
title: "RPC・gRPC"
date: 2026-05-07
tags:
  - Web
  - API
  - Backend
related:
  - "[[API]]"
  - "[[REST API]]"
  - "[[HTTP・HTTPS]]"
  - "[[マイクロサービスアーキテクチャ]]"
---

## 概要

RPC（Remote Procedure Call）は，ネットワーク越しに別プロセスの関数をローカル関数のように呼び出すプロトコル．gRPC は Google が開発したオープンソースの高性能 RPC フレームワークで，HTTP/2 と Protocol Buffers を基盤とする．

## 詳細

### gRPC の特徴

| 特徴 | 説明 |
|------|------|
| **トランスポート** | HTTP/2（多重化・ヘッダ圧縮・バイナリフレーミング） |
| **シリアライズ** | Protocol Buffers（Protobuf）—バイナリ形式で JSON より高速・コンパクト |
| **スキーマ定義** | `.proto` ファイルでサービスとメッセージを定義，コード自動生成 |
| **双方向ストリーミング** | クライアント／サーバ双方から独立してストリームを送受信可能 |
| **多言語対応** | Go, Java, Python, C++, Rust 等に公式サポート |

### 通信パターン

| パターン | 説明 |
|---------|------|
| Unary | 1 リクエスト → 1 レスポンス（通常の関数呼び出し） |
| Server Streaming | 1 リクエスト → レスポンスのストリーム |
| Client Streaming | リクエストのストリーム → 1 レスポンス |
| Bidirectional Streaming | 双方向ストリーム |

### REST との比較

| 観点 | REST | gRPC |
|------|------|------|
| プロトコル | HTTP/1.1（主に） | HTTP/2 |
| ペイロード | JSON（テキスト） | Protobuf（バイナリ） |
| 速度 | 中 | 高速（バイナリ + 多重化） |
| 型安全性 | 低（OpenAPI が必要） | 高（.proto で自動生成） |
| ブラウザ対応 | ネイティブ対応 | grpc-web が必要 |
| 用途 | 汎用 Web API | マイクロサービス内部通信 |

## ポイント

- マイクロサービス間通信での標準的な選択肢
- Protobuf によりペイロードが JSON 比で 3-10 倍小さい場合がある
- ブラウザから直接呼ぶには grpc-web または [[REST API]] への変換レイヤが必要
- スキーマ変更時の後方互換性管理が重要

## 関連項目

- [[API]]
- [[REST API]]
- [[HTTP・HTTPS]]
- [[マイクロサービスアーキテクチャ]]
- [[シリアライズ]]

## 参考

- [gRPC 公式サイト](https://grpc.io/)
- [gRPC - Wikipedia](https://en.wikipedia.org/wiki/GRPC)
- [What Is gRPC? - IBM](https://www.ibm.com/think/topics/grpc)
