---
title: "HDFS"
date: 2026-05-07
tags:
  - Cloud
  - DB
related:
  - "[[Apache Hadoop]]"
  - "[[Apache Spark]]"
  - "[[データレイク]]"
  - "[[オブジェクトストレージ]]"
  - "[[ブロックストレージ]]"
  - "[[Apache Druid]]"
---

## 概要

HDFS（Hadoop Distributed File System）は，Apache Hadoop プロジェクトの中核をなす分散ファイルシステム．大規模データを安価な汎用サーバのクラスタに分散保存し，高スループット・高耐障害性を実現する．

## 詳細

### アーキテクチャ

```
クライアント
  ↕ HDFS Client
NameNode（メタデータ管理・1〜2 台）
  ↕
DataNode × N（実データの保存・多数台）
```

| コンポーネント | 役割 |
|-------------|------|
| **NameNode** | ファイルシステムのメタデータ（ブロック配置マップ）を管理 |
| **DataNode** | 実データブロックを保存；NameNode の指示に従いレプリカを管理 |
| **Secondary NameNode** | NameNode のチェックポイントを定期保存（HA ではないことに注意） |

### データ管理

- **ブロックサイズ**: デフォルト 128MB（設定変更可）—小ファイルが多いと非効率
- **レプリケーション係数**: デフォルト 3 コピー（ラック障害にも対応できる配置）
- **Write Once, Read Many**: ファイルは追記か上書きのみ，ランダム書き込み不可

### HDFS vs オブジェクトストレージ

| 観点 | HDFS | [[オブジェクトストレージ]]（S3 等） |
|------|------|---------------------|
| 処理特性 | コロケーション（データ近くで処理） | コンピュート・ストレージ分離 |
| スケール | クラスタ規模に依存 | ほぼ無限 |
| 運用コスト | 高（NameNode 管理等） | 低（マネージド） |
| レイテンシ | 低（ローカルネットワーク） | 中（ネットワーク越し） |

クラウド移行では HDFS を S3 / GCS / ADLS 等に置き換えるケースが多い．

## ポイント

- [[Apache Hadoop]] のエコシステム（Hive, Spark, HBase 等）から利用するのが前提
- NameNode の単一障害点（SPOF）問題は HA 構成（Active/Standby）で解消できる
- クラウドネイティブ環境では HDFS より [[オブジェクトストレージ]] への移行が主流

## 関連項目

- [[Apache Hadoop]]
- [[Apache Spark]]
- [[データレイク]]
- [[オブジェクトストレージ]]
- [[ブロックストレージ]]
- [[NAS]]
- [[Apache Druid]]

## 参考

- [Apache HDFS Architecture Guide](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html)
- [HDFS - Wikipedia](https://en.wikipedia.org/wiki/Apache_Hadoop#HDFS)
