---
title: "Google Cloud Pub/Sub"
date: 2026-05-10
tags:
  - Cloud
  - GCP
related:
  - "[[メッセージキュー]]"
  - "[[イベントストリーミング]]"
  - "[[Amazon Kinesis]]"
  - "[[Apache Kafka]]"
  - "[[Apache Pulsar]]"
---

## 概要

Google Cloud Pub/Subは，GCPが提供するサーバーレスのフルマネージドメッセージングサービス．Publisher がメッセージを Topic に送信し，Subscriber が非同期で受信するパブリッシュ・サブスクライブモデルを採用．グローバルに低レイテンシで配信でき，疎結合なアーキテクチャを実現する．

## 詳細

### 基本コンセプト

```
Publisher → Topic → Subscription → Subscriber
                 ↓
            Message Storage
```

- **Topic**: メッセージの名前付きチャネル
- **Subscription**: Topic に紐付く配信設定（1:N が可能）
- **Message**: データ本体＋属性（key/value メタデータ）
- **Acknowledgement**: Subscriber が受信完了を通知するまでメッセージを再送

### 配信モード

| モード | 説明 | ユースケース |
|--------|------|------------|
| プル（Pull） | Subscriber が能動的に取得 | バッチ処理，スケール制御 |
| プッシュ（Push） | Pub/Sub が HTTP エンドポイントへ配信 | サーバーレス（Cloud Run，Cloud Functions） |

### 主な特徴

- **グローバル配信**: Publisher と Subscriber がどのリージョンにいても自動ルーティング
- **スケーラビリティ**: サーバーレスで自動スケール，プロビジョニング不要
- **最低1回配信（at-least-once）**: 重複受信の可能性があるため，消費者側の冪等性が必要
- **メッセージ保持**: 未 ACK メッセージを最長7日間保持
- **フィルタリング**: 属性ベースのサーバーサイドフィルタリングで不要なメッセージを除外

### 2025年の新機能

- **単一メッセージ変換（SMT）**: メッセージがパイプラインを流れる際にリアルタイムで変換・フィルタ・検証を実行可能（2025年6月リリース）

### [[Apache Kafka]] との比較

| 比較項目 | Cloud Pub/Sub | [[Apache Kafka]] |
|---------|--------------|--------------|
| 管理 | フルマネージド・サーバーレス | セルフホスト or マネージド |
| メッセージ順序 | Subscription 内でオプション | Partition 内で保証 |
| メッセージリプレイ | Snapshot 機能で可 | Offset リセットで可 |
| レイテンシ | 数百ms〜秒 | ミリ秒〜数百ms |
| 適合環境 | GCP ネイティブ | マルチクラウド / オンプレ |

## ポイント

- GCP の他サービス（BigQuery，Dataflow，Cloud Run 等）との統合が強力
- メッセージ順序を保証したい場合は「順序指定メッセージ」機能を有効にする
- Pub/Sub Lite は低コスト版（ゾーン限定，容量プロビジョニングあり）

## 関連項目

- [[メッセージキュー]]
- [[イベントストリーミング]]
- [[Amazon Kinesis]]
- [[Apache Kafka]]
- [[Apache Pulsar]]

## 参考

- [Pub/Sub とは - Google Cloud](https://docs.cloud.google.com/pubsub/docs/overview?hl=ja)
- [Cloud Pub/Sub の特徴とユースケース - G-gen](https://g-gen.co.jp/useful/google-service/22226/)
- [新しい Pub/Sub 単一メッセージ変換 - Google Cloud Blog](https://cloud.google.com/blog/ja/products/data-analytics/pub-sub-single-message-transforms?hl=ja)
