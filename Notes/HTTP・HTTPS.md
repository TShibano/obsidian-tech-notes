---
title: "HTTP・HTTPS"
date: 2026-05-06
tags:
  - Web
  - Backend
related:
  - "[[API]]"
  - "[[SSO]]"
---

## 概要

HTTP（HyperText Transfer Protocol）はWebにおけるデータ通信の基盤プロトコル．HTTPSはTLS（Transport Layer Security）で暗号化したHTTP通信で，現代のWebでは標準．

## 詳細

### HTTPとHTTPSの違い

| 項目 | HTTP | HTTPS |
|------|------|-------|
| 暗号化 | なし（平文） | TLS で暗号化 |
| ポート | 80 | 443 |
| 証明書 | 不要 | サーバー証明書が必要 |
| 用途 | ローカル開発，内部通信 | 公開 Web サービス全般 |

### TLS の役割

TLS（旧称 SSL）がHTTPSを支える暗号化プロトコル．以下の3点を保証する:

1. **機密性**: 通信内容の暗号化（盗聴防止）
2. **完全性**: データ改ざんの検知
3. **認証**: サーバーの正当性確認（デジタル証明書）

### TLSハンドシェイクの概要

1. クライアントが対応する暗号スイートを提示
2. サーバーが証明書を送付
3. クライアントが証明書を検証（CA の署名を確認）
4. セッション鍵を交換し，暗号化通信開始

### HTTPバージョン

| バージョン | 特徴 |
|-----------|------|
| HTTP/1.1 | テキストプロトコル，1リクエストずつ直列処理 |
| HTTP/2 | バイナリプロトコル，多重化（ヘッダ圧縮，サーバープッシュ） |
| HTTP/3 | QUIC（UDP ベース）で低レイテンシを実現 |

## ポイント

- SSL は脆弱性のため廃止済み．現在は TLS 1.2 以上が推奨
- HTTPS でも証明書の検証が不正であれば中間者攻撃は可能
- Let's Encrypt により無料で証明書取得が可能になり HTTPS が普及

## 関連項目

- [[API]]
- [[SSO]]
- [[FTP]]

## 参考

- [HTTPSプロトコルはなぜ必要？HTTP・SSLとの違いや通信の仕組みを解説](https://group.gmo/security/ciphersecurity/http-https/blog/https-protocol/)
- [HTTPS - Wikipedia](https://en.wikipedia.org/wiki/HTTPS)
- [Why is HTTP not secure? | Cloudflare](https://www.cloudflare.com/learning/ssl/why-is-http-not-secure/)
