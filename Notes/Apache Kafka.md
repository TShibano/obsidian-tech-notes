---
title: "Apache Kafka"
date: 2026-02-26
tags:
  - DevOps
  - Cloud
related:
  - "[[Apache Spark]]"
  - "[[Apache Airflow]]"
  - "[[データ基盤]]"
  - "[[Docker]]"
  - "[[Kubernetes]]"
  - "[[データアーキテクチャ]]"
---

## 概要

Apache Kafkaは、LinkedIn が開発しApache Software Foundation に寄贈したオープンソースの分散イベントストリーミングプラットフォーム。高スループット・低レイテンシ・耐障害性を兼ね備え、1日あたり数兆件のイベントを処理可能。世界のFortune 100企業の80%以上が採用する、データエンジニアリングの中核インフラ。

## 詳細

### コアコンセプト

- **Event（イベント）**: Kafka が扱うデータの基本単位。「何かが起きた」という事実を記録（例：ユーザーがクリックした、センサーが値を送信した）
- **Topic（トピック）**: イベントを分類・格納するカテゴリ。ファイルシステムのフォルダに相当
- **Partition（パーティション）**: トピックを複数に分割し並列処理を実現する。スループットのスケールアウト単位
- **Broker（ブローカー）**: Kafka クラスタを構成するサーバー。データ（パーティション）を保持し、クライアントリクエストを処理
- **Producer（プロデューサー）**: Kafka にイベントを書き込むクライアント
- **Consumer（コンシューマー）**: Kafka からイベントを読み込むクライアント
- **Consumer Group**: 複数コンシューマーで並列読み込みする仕組み。グループ内の各パーティションは1つのコンシューマーのみが担当

### アーキテクチャ

```
Producer → [Topic: user-events]  → Consumer Group A (ストリーム処理)
                  ↓                → Consumer Group B (DB書き込み)
           Partition 0: Broker 1   → Consumer Group C (分析)
           Partition 1: Broker 2
           Partition 2: Broker 3
```

**レプリケーション**: 各パーティションは複数ブローカーにコピーされる（通常 replication factor=3）。ブローカー障害時も自動的にフェイルオーバー。

**KRaft モード**: Kafka 3.x から ZooKeeper を廃止し、Kafka 自身が分散合意を管理する KRaft モードに移行中（2.x 以前は ZooKeeper が必要だった）。

### 主な性能特性

- レイテンシ: 2ms 以下
- スループット: クラスタ全体で1秒あたり数百万メッセージ
- スケール: 1クラスタで1,000ブローカーまで

### データエンジニアリングでの活用パターン

| ユースケース | 説明 |
|-------------|------|
| **リアルタイムデータパイプライン** | RDBMS・マイクロサービス → Kafka → DWH/データレイク |
| **イベント駆動アーキテクチャ** | システム間の非同期連携。疎結合化 |
| **ストリーム処理** | Kafka Streams や Flink と組み合わせて、読み取りながらリアルタイム変換 |
| **ログ集約** | 複数システムのログを一元集約 |
| **CDC（Change Data Capture）** | DBの変更をリアルタイムで Kafka に流す（Debezium等） |

### エコシステム

- **Kafka Streams**: Kafka 内のデータをリアルタイム処理する Java/Scala ライブラリ（追加インフラ不要）
- **Kafka Connect**: 外部システム（DB, S3等）との接続プラグイン集
- **Schema Registry**: Avro/JSON スキーマを管理し、プロデューサー・コンシューマー間のスキーマ互換性を保証
- **Confluent Platform**: Kafka の商用ディストリビューション（マネージドサービスも提供）

## ポイント

- **プロデューサーとコンシューマーの分離**: それぞれが独立してスケール可能。システム全体の疎結合化に貢献
- **イベントの永続化**: ディスクに書き込みつつ高速（OS のページキャッシュを活用）。保持期間を設定でき、同じデータを複数回読み直せる
- **順序保証はパーティション内のみ**: トピック全体での順序は保証されない。順序が必要なデータは同一パーティションに送る
- **Exactly-Once Semantics**: Kafka Streams では exactly-once 処理が保証できる
- **監視**: Consumer Lag（コンシューマーの遅延）を監視することで、処理能力が追いついているか把握できる

## 関連項目

- [[Apache Spark]] — Spark Structured Streaming と組み合わせてストリーム処理を行う
- [[Apache Airflow]] — Airflow DAG で Kafka への書き込み・読み込みを管理
- [[データ基盤]] — リアルタイムデータ取り込みの中核インフラとして機能
- [[Docker]] — Kafka クラスタのコンテナ化
- [[Kubernetes]] — 本番環境での Kafka のオーケストレーション

## 参考

- [Apache Kafka公式: Introduction](https://kafka.apache.org/intro/)
- [Confluent Docs: Introduction to Apache Kafka](https://docs.confluent.io/kafka/introduction.html)
