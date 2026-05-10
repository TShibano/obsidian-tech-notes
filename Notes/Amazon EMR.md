---
title: "Amazon EMR"
date: 2026-05-10
tags:
  - Cloud
  - AWS
related:
  - "[[Apache Hadoop]]"
  - "[[Apache Spark]]"
  - "[[Amazon S3]]"
  - "[[データパイプライン]]"
  - "[[Databricks]]"
---

## 概要

Amazon EMR（Elastic MapReduce）は，AWSが提供するマネージドビッグデータ処理プラットフォーム．Apache Hadoop，Apache Spark，Hive，Presto，HBase 等のオープンソースフレームワークを，EC2 クラスタまたはサーバーレスで実行できる．クラスタのプロビジョニング・設定・パッチ適用をAWSが管理する．

## 詳細

### アーキテクチャ

**クラスタ構成:**

| ノード種別 | 役割 |
|-----------|------|
| Primary Node（Master） | クラスタ全体のリソース管理，ジョブスケジューリング |
| Core Node | データ処理 ＋ HDFS データ格納 |
| Task Node | データ処理のみ（HDFS なし，スポットインスタンスに適合） |

**ストレージ選択肢:**

| 方式 | 特徴 |
|------|------|
| HDFS（クラスタ内） | 高速 I/O，クラスタ削除でデータ消滅（エフェメラル） |
| EMRFS（S3） | 永続化，コンピュートとストレージを分離 |

### サポートするフレームワーク

- **[[Apache Spark]]**: インメモリ分散処理，機械学習（MLlib），ストリーミング
- **[[Apache Hadoop]]**: MapReduce ベースのバッチ処理
- **Apache Hive**: SQL ライクな大規模クエリ
- **Presto / Trino**: インタラクティブ SQL
- **HBase**: NoSQL カラム型ストア
- **Apache Flink**: ストリーム処理

### デプロイメントモード

| モード | 説明 | 用途 |
|--------|------|------|
| EMR on EC2 | 従来のEC2クラスタ | 長時間バッチ，細かい制御 |
| EMR on EKS | Kubernetes 上で実行 | コンテナ化ワークロード |
| EMR Serverless | サーバーレス，自動スケール | アドホッククエリ，開発環境 |

### [[Databricks]] との比較

| 比較項目 | Amazon EMR | [[Databricks]] |
|---------|-----------|------------|
| 管理 | AWS マネージド | Databricks マネージド |
| 統合 | AWS エコシステム | マルチクラウド |
| ノートブック | EMR Studio | Databricks Notebooks |
| Delta Lake | 手動設定 | ネイティブ統合 |
| 運用コスト | 低め（EC2 直接） | 高め（DBU 課金） |

## ポイント

- S3 を永続ストレージとしてコンピュートとストレージを分離するのが現代のベストプラクティス
- スポットインスタンスの Task Node を活用するとコストを大幅削減できる
- EMR Serverless は小〜中規模のアドホック処理に適しているが，大規模ジョブではコスト試算が必要

## 関連項目

- [[Apache Hadoop]]
- [[Apache Spark]]
- [[Amazon S3]]
- [[データパイプライン]]
- [[Databricks]]

## 参考

- [Amazon EMR Features - AWS](https://www.amazonaws.cn/en/elasticmapreduce/features/)
- [Getting Started with AWS EMR - DataCamp](https://www.datacamp.com/tutorial/amazon-emr)
- [AWS EMR vs Databricks - Chaos Genius](https://www.chaosgenius.io/blog/aws-emr-vs-databricks/)
