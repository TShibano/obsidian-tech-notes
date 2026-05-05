---
title: "Apache Hudi"
date: 2026-05-05
tags:
  - DB
  - Cloud
  - データアーキテクチャ
related:
  - "[[Apache Iceberg]]"
  - "[[Delta Lake]]"
  - "[[データレイクハウス]]"
  - "[[データアーキテクチャ]]"
  - "[[リアルタイム処理]]"
  - "[[ACID特性]]"
---

## 概要

Apache Hudi（Hadoop Upserts Deletes and Incrementals）は，データレイク上でレコードレベルの挿入・更新・削除と ACID トランザクションを実現するオープンソースのデータレイクハウスプラットフォームである．2016年に Uber が開発しオープンソース化し，ストリーミングネイティブな増分処理に特化している．

## 詳細

### 3大オープンテーブルフォーマットの比較

| 項目 | Apache Hudi | [[Apache Iceberg]] | [[Delta Lake]] |
|------|------------|-------------------|---------------|
| 発祥 | Uber（2016） | Netflix（2018） | Databricks（2019） |
| 増分処理 | 最も強い（設計思想の中心） | 対応 | 対応 |
| ストリーミング統合 | 特に優れる（Kafka等との直接統合） | 対応 | Databricks が最適化 |
| ACID | 対応 | 対応 | 対応 |
| タイムトラベル | 対応 | 対応（スナップショット管理が強力） | 対応 |
| エコシステム | AWS（EMR，Glue）が主力 | AWS/GCP/Azure で広く採用中 | Databricks エコシステム |
| 2026年の位置 | ストリーミング特化ニッチ | デファクト化が加速 | Databricks 内では支配的 |

### 主な特徴

#### ストリーミングネイティブな増分処理

Hudi の最大の強みは増分処理と Change Data Capture（CDC）サポートである：

```
ソース（Kafka / CDC）
    ↓ 増分データのみ
Hudi テーブル（レコードレベルの UPSERT/DELETE）
    ↓ 増分変更のみを下流へ伝播
下流サービス（低レイテンシ更新）
```

「フル再ロードではなく変更分のみを効率的に処理する」というバッチ処理の再発明が Hudi の哲学．

#### 2つのテーブルタイプ

| タイプ | 特徴 | 適する用途 |
|--------|------|-----------|
| **Copy-on-Write（COW）** | 書き込み時にファイルを再書き込み．読み取りが高速 | バッチ重視・読み取り多 |
| **Merge-on-Read（MOR）** | 差分をログファイルに追記，読み取り時にマージ | ストリーミング・書き込み多 |

#### ACID トランザクション

タイムライン（Timeline）と呼ばれる仕組みでトランザクションを管理し，部分的な書き込み失敗からのロールバックとリカバリを保証する．

### Hudi のテーブルサービス

| サービス | 説明 |
|---------|------|
| **Compaction** | MOR の差分ログを COW 形式にまとめ，読み取り性能を改善 |
| **Clustering** | 小さなファイルを統合してクエリ性能を最適化（Small File Problem 解消） |
| **Clean** | 古いバージョンのファイルを削除してストレージを節約 |
| **Indexing** | Bloom Filter・Bucket Index等でレコード検索を高速化 |

### ユースケース

- **CDC パイプライン**: データベースの変更をリアルタイムでデータレイクに同期
- **GDPR 対応（Right to be Forgotten）**: レコードレベルの削除が必要な場合
- **ストリーミング ETL**: Kafka から直接 Hudi テーブルに書き込む低レイテンシパイプライン
- **Uber の実績**: Hudi 開発元として数十 PB 規模のデータを管理

## ポイント

- Iceberg・Delta Lake と並ぶ3大オープンテーブルフォーマットの一つ
- 増分処理（Incremental Processing）に最も特化しており，CDC 統合が強み
- 2026年はデファクト化が進む [[Apache Iceberg]] に対して，Hudi はストリーミングニッチで存在感を維持
- Uber・GE・Disney・ByteDance など大規模データ企業で採用
- AWS EMR/Glue との統合が特に充実しており，AWS 環境での採用が多い

## 関連項目

- [[Apache Iceberg]] — デファクト化が加速するオープンテーブルフォーマット（競合かつ相互参照）
- [[Delta Lake]] — Databricks エコシステムで支配的なオープンテーブルフォーマット
- [[データレイクハウス]] — Hudi が構成コンポーネントとして機能するアーキテクチャ
- [[データアーキテクチャ]] — Hudi が含まれるデータ基盤設計全体
- [[リアルタイム処理]] — Hudi がストリーミングデータの格納先として連携
- [[ACID特性]] — Hudi が保証するトランザクション特性

## 参考

- [Apache Hudi 公式サイト](https://hudi.apache.org/)
- [更新可能なデータレイクを構築するテーブルフォーマットApache Hudiについて | Repro Tech Blog](https://tech.repro.io/entry/2024/07/26/141233)
- [Apache Hudi を用いてレコード単位で削除可能なデータレイクを構築した話 | Yahoo! JAPAN Tech Blog](https://techblog.yahoo.co.jp/entry/2022052530303179/)
- [データレイクハウスとは？Delta Lake・Apache Iceberg・Hudiの比較 | renue](https://renue.co.jp/posts/data-lakehouse-delta-lake-iceberg-hudi-comparison-guide)
