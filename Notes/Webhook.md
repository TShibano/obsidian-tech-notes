---
title: "Webhook"
date: 2026-05-07
tags:
  - Web
  - API
  - Backend
related:
  - "[[API]]"
  - "[[REST API]]"
  - "[[HTTP・HTTPS]]"
---

## 概要

Webhook は，あるサービスで特定のイベントが発生したとき，あらかじめ登録された URL に HTTP POST リクエストを自動送信するコールバック機構．「リバース API」とも呼ばれ，ポーリングの代替としてリアルタイムな通知を実現する．

## 詳細

### 動作フロー

```
イベント発生
  ↓
送信側サービスが登録 URL に HTTP POST（JSON ペイロード）
  ↓
受信側サービスがリクエストを処理
  ↓
200 OK を返却（タイムアウト前に応答）
```

### ポーリングとの比較

| 観点 | ポーリング | Webhook |
|------|---------|---------|
| 通信モデル | プル（定期問い合わせ） | プッシュ（イベント駆動） |
| レイテンシ | ポーリング間隔に依存 | ほぼリアルタイム |
| サーバ負荷 | 高い（無駄リクエスト） | 低い |
| 実装複雑度 | 低 | 中（エンドポイント公開が必要） |

### セキュリティ

- **HMAC 署名**: 送信側が共有シークレットで署名し，受信側が検証する（`X-Hub-Signature` ヘッダ等）
- **HTTPS 必須**: ペイロードの盗聴防止
- **冪等処理**: 同一イベントの重複配信に備え，冪等な処理を実装する

### 実装の注意点

- 受信処理は即座に 200 を返し，重い処理はキューに委ねる（タイムアウト対策）
- 失敗した Webhook のリトライを考慮した設計が必要
- べき等キーでの重複排除を実装する

## ポイント

- GitHub, Stripe, Slack など多くのサービスが Webhook を提供
- [[REST API]] のポーリングより効率的なイベント通知手段
- 受信側が公開エンドポイントを持つ必要があるため，ファイアウォール内部での利用は困難

## 関連項目

- [[API]]
- [[REST API]]
- [[HTTP・HTTPS]]
- [[冪等]]

## 参考

- [What is a webhook? - Red Hat](https://www.redhat.com/en/topics/automation/what-is-a-webhook)
- [Webhooks and Event-Driven Architecture - apidog](https://apidog.com/blog/comprehensive-guide-to-webhooks-and-eda/)
