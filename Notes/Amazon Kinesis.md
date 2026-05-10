---
title: "Amazon Kinesis"
date: 2026-05-10
tags:
  - Cloud
  - AWS
related:
  - "[[イベントストリーミング]]"
  - "[[Apache Kafka]]"
  - "[[Apache Pulsar]]"
  - "[[Amazon S3]]"
  - "[[変更データキャプチャ]]"
---

## 概要

Amazon Kinesisは，AWSが提供するリアルタイムストリーミングデータの収集・処理・分析プラットフォーム．Kinesis Data Streams，Data Firehose，Data Analytics の3サービスで構成され，ログ・クリックストリーム・IoTデータ等のリアルタイム処理に使われる．

## 詳細

### 主要サービス

| サービス | 役割 |
|---------|------|
| Kinesis Data Streams | ストリームの収集・保持（カスタム処理向け） |
| Kinesis Data Firehose | 収集からS3/Redshift/OpenSearch等への自動配信 |
| Kinesis Data Analytics | SQL/Apache Flink によるリアルタイム分析 |

### Kinesis Data Streams の仕組み

- **シャード（Shard）**: ストリームの基本単位．1シャードあたり取り込み1MB/s・1000レコード/s，読み出し2MB/s
- **レコード**: 各メッセージの単位（シーケンス番号・パーティションキー・データ本体）
- **保持期間**: デフォルト24時間，最長365日
- **Put-to-get遅延**: 通常1秒未満

```
Producer → Kinesis Data Streams (Shards) → Consumer (Lambda, EC2, EMR...)
```

### スケーリング

- シャード数を増減してスループットを調整
- **On-Demand モード**: 自動スケール，瞬時に10GB/s または1000万イベント/sまで対応
- **Provisioned モード**: シャード数を手動管理，コスト予測可能

### 2025年の主要アップデート

- **On-Demand Advantage モード**: ウォームアップ済みスループット確保，コスト60%削減
- **レコードサイズ10MiB対応**: 従来の1MBから10倍に拡大（CDC・生成AIワークロード向け）
- デフォルトシャード上限が1アカウントあたり20,000まで引き上げ

### [[Apache Kafka]] との比較

| 比較項目 | Amazon Kinesis | [[Apache Kafka]] |
|---------|---------------|--------------|
| 管理 | フルマネージド | セルフホスト or MSK |
| スケール単位 | シャード（手動 or 自動） | パーティション |
| 保持期間 | 最長365日 | 設定次第 |
| エコシステム | AWS サービスとの統合 | 幅広いコネクタ |
| レイテンシ | 1秒未満 | ミリ秒〜秒 |

## ポイント

- AWS ネイティブな環境では [[Apache Kafka]] より運用負荷が低い
- 大量の小さいメッセージより，バッチ化した大きなメッセージが効率的
- 下流に [[Amazon S3]]，Amazon Redshift，OpenSearch 等と組み合わせるのが一般的

## 関連項目

- [[イベントストリーミング]]
- [[Apache Kafka]]
- [[Apache Pulsar]]
- [[Amazon S3]]
- [[変更データキャプチャ]]

## 参考

- [Amazon Kinesis 公式](https://aws.amazon.com/kinesis/)
- [Amazon Kinesis Data Streams - AWS docs](https://docs.aws.amazon.com/streams/latest/dev/introduction.html)
- [Kinesis On-Demand Advantage - AWS Blog](https://aws.amazon.com/jp/blogs/news/kinesis-on-demand-advantage-saves-60-on-streaming-costs/)
