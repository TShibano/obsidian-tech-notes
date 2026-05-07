---
title: "Apache Druid"
date: 2026-05-07
tags:
  - DB
  - Cloud
related:
  - "[[OLAP]]"
  - "[[Apache Kafka]]"
  - "[[リアルタイム処理]]"
  - "[[Apache Hadoop]]"
  - "[[HDFS]]"
  - "[[BigQuery]]"
  - "[[Snowflake]]"
---

## 概要

Apache Druid は，リアルタイム OLAP クエリに特化した高性能分散データベース．ストリームデータの即時取り込みと低レイテンシ集計クエリを両立し，時系列イベントデータの分析に強みを持つ．

## 詳細

### アーキテクチャ

Druid は役割の異なる複数のプロセスで構成される：

| プロセス | 役割 |
|---------|------|
| **Coordinator** | データ可用性の管理，ルール適用 |
| **Overlord** | インジェスションタスクの管理 |
| **Broker** | クエリのルーティング・マージ |
| **Historical** | 不変のセグメントデータを保持・提供 |
| **MiddleManager** | リアルタイムインジェスション処理 |
| **Router** | Broker へのクエリ転送 |

Deep Storage（HDFS, S3 等）にセグメントを永続化し，障害時に自動リカバリする．

### データ取り込み

| 方式 | 説明 |
|------|------|
| ストリーミング | [[Apache Kafka]] / Amazon Kinesis からリアルタイム取り込み |
| バッチ | HDFS, S3, GCS, ローカルファイルから取り込み |

取り込んだデータは自動的に列指向・時間インデックス・辞書圧縮・ビットマップインデックスを適用してセグメント化される．

### 性能の特徴

- **ビットマップインデックス**: 多次元フィルタリングを高速化
- **列指向ストレージ**: 集計クエリに必要な列だけ読む
- **事前集計（ロールアップ）**: 取り込み時に時間粒度でデータを集約してサイズ削減
- **並列スキャン**: スキャッター・ギャザー方式でクラスタ全体を並列検索

### 他 OLAP ツールとの比較

| 観点 | Apache Druid | [[BigQuery]] | [[Snowflake]] |
|------|-------------|------------|-------------|
| リアルタイム取り込み | 得意（サブ秒） | 弱い（ストリーミング制約） | 弱い |
| クエリ速度 | 非常に高速（事前インデックス） | 高速（大規模） | 高速 |
| 運用コスト | 高（自己管理） | 低（フルマネージド） | 低 |
| SQL 対応 | SQL（Druid SQL） | 標準 SQL | 標準 SQL |

## ポイント

- 大量イベントの集計ダッシュボード（広告, IoT, ログ分析等）に強い
- ユーザーが直接叩くインタラクティブクエリ（クエリ結果を 1 秒以内に返す）に最適化
- [[Apache Kafka]] との組み合わせがリアルタイム分析パイプラインの定番

## 関連項目

- [[OLAP]]
- [[Apache Kafka]]
- [[リアルタイム処理]]
- [[Apache Hadoop]]
- [[HDFS]]
- [[BigQuery]]
- [[Snowflake]]

## 参考

- [Apache Druid 公式サイト](https://druid.apache.org/)
- [Introduction to Apache Druid](https://druid.apache.org/docs/latest/design/index.html)
- [Apache Druid - Wikipedia](https://en.wikipedia.org/wiki/Apache_Druid)
