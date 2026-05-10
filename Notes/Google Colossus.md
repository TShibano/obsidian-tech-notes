---
title: "Google Colossus"
date: 2026-05-10
tags:
  - Cloud
  - GCP
related:
  - "[[HDFS]]"
  - "[[BigQuery]]"
  - "[[Apache Hadoop]]"
  - "[[オブジェクトストレージ]]"
---

## 概要

Google Colossusは，Googleが内部で使用する分散ファイルシステム（DFS）．2010年頃にGFS（Google File System）の後継として開発され，Googleのデータセンター全体のストレージ基盤となっている．BigTable，Spanner，BigQueryなどのGoogle社内システムはすべてColossusの上で動作する．

## 詳細

### GFSからColossusへの進化

| 比較項目 | GFS（2003） | Colossus |
|---------|-----------|---------|
| メタデータ管理 | 単一マスターノード | BigTableベースの分散メタデータ |
| スケール | 数千台程度 | 数万〜数十万台以上 |
| メタデータスケール | GFS比で100倍以上 | エクサバイト級 |
| 耐障害性 | マスター単一障害点あり | 分散メタデータで排除 |

### アーキテクチャ

```
Client → Colossus Client Library
            ↓
   Curators（メタデータ管理 via BigTable）
            ↓
   D-files（ストレージデーモン，各ノード上）
            ↓
   物理ストレージ（HDD / SSD / Persistent Disk）
```

- **Curators**: メタデータサーバ群．BigTableにファイルメタデータを格納することで，GFSの単一マスターボトルネックを解消
- **D-files（Disk files）**: 各ストレージノード上で動作するデーモン．データブロックの読み書きを担当

### データの配置戦略

- **ホットデータ**: 書き込まれた直後はクラスタ全体のドライブに均等分散
- **コールドデータ**: 時間が経つとより大容量のドライブに移動（コスト最適化）
- [[BigQuery]] のストレージは Colossus 上の **Capacitor** フォーマット（カラム型）で管理

### BigTableとの関係

- **BigTableはColossus上に動作**: BigTableのタブレットデータは Colossus の SSTable として格納
- Colossus の分散ファイルシステムにより，BigTable はコンピュートノードから SSTable をほぼ瞬時に再割り当て可能
- この分離設計がBigTableの高可用性と迅速なフェイルオーバーを実現

### Googleの公開情報

Colossusはクローズドシステムだが，2023年にGoogleがアーキテクチャの一部を公開．ストレージコストを最適化しながらエクサバイト規模のデータを管理する仕組みを明らかにした．

## ポイント

- Colossus はGoogleのすべてのストレージ基盤（GCS，BigQuery，Gmail等）の根幹
- [[HDFS]] がマスターノードのボトルネック問題を持つのに対し，Colossusは BigTable によるメタデータ分散で解決
- Google Cloud Storage（GCS）のバックエンドも Colossus

## 関連項目

- [[HDFS]]
- [[BigQuery]]
- [[Apache Hadoop]]
- [[オブジェクトストレージ]]

## 参考

- [A peek behind Colossus, Google's file system - Google Cloud Blog](https://cloud.google.com/blog/products/storage-data-transfer/a-peek-behind-colossus-googles-file-system)
- [Colossus: Google's File System - Shambhavi Shandilya](https://shambhavishandilya.medium.com/colossus-googles-file-system-baced846d9b7)
- [How Google stores Exabytes of Data - Quastor](https://blog.quastor.org/p/google-stores-exabytes-data)
