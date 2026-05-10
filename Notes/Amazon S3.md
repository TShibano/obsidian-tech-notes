---
title: "Amazon S3"
date: 2026-05-10
tags:
  - Cloud
  - AWS
related:
  - "[[オブジェクトストレージ]]"
  - "[[ハイブリッドオブジェクトストレージ]]"
  - "[[Amazon Kinesis]]"
  - "[[Apache Hadoop]]"
---

## 概要

Amazon S3（Simple Storage Service）はAWSが提供するオブジェクトストレージサービス．99.999999999%（11 nines）の耐久性，事実上無制限のスケーラビリティ，豊富なストレージクラスを提供し，業界標準のクラウドオブジェクトストレージとして広く使われる．

## 詳細

### 仕組み

- データは**オブジェクト**として**バケット**に格納される
- 各オブジェクトは**データ本体・メタデータ・一意のキー（パス）**で構成
- バケットはリージョン単位で作成し，複数のアベイラビリティゾーン（AZ）に冗長化
- HTTP/REST API でアクセス（`PUT`, `GET`, `DELETE`, `HEAD`）

### ストレージクラス

| クラス | アクセス頻度 | 用途 |
|--------|------------|------|
| S3 Standard | 高頻度 | アクティブデータ，Webコンテンツ |
| S3 Standard-IA | 低頻度 | バックアップ，DR |
| S3 One Zone-IA | 低頻度（単一AZ） | 再生成可能なデータ |
| S3 Glacier Instant Retrieval | 低頻度・即時取得 | 月次アーカイブ |
| S3 Glacier Flexible Retrieval | 低頻度・数時間 | 長期アーカイブ |
| S3 Glacier Deep Archive | 最低頻度 | 7〜10年保存 |
| S3 Intelligent-Tiering | 自動最適化 | アクセスパターン不明 |

### 主な機能

- **バージョニング**: オブジェクトの複数バージョンを保持
- **ライフサイクルポリシー**: 期間経過後に自動でストレージクラス変更や削除
- **S3 Event Notifications**: オブジェクト操作をトリガーにLambda等を起動
- **S3 Select / Glacier Select**: オブジェクト内のCSV/JSON/Parquetを直接クエリ
- **S3 Replication (CRR/SRR)**: クロスリージョン / 同一リージョンレプリケーション
- **Block Public Access**: デフォルトでパブリックアクセスをブロック

### データエンジニアリングでの用途

- データレイクのストレージ層（[[Apache Hadoop]] EMRFS，[[Delta Lake]]，[[Apache Iceberg]] 等）
- ETL の中間ステージング
- [[Amazon Kinesis]] からのストリームデータの保存先
- [[Apache Parquet]] / [[Apache Avro]] 等のカラム型フォーマットで格納

## ポイント

- コスト: ストレージ・リクエスト・データ転送の従量課金（AWSへの転送は無料）
- S3 は「ファイルシステム」ではなく「オブジェクトストア」のため，部分書き込み・アペンドは非対応
- MinIO は S3 互換 API を持つオンプレミス代替

## 関連項目

- [[オブジェクトストレージ]]
- [[ハイブリッドオブジェクトストレージ]]
- [[Amazon Kinesis]]
- [[Apache Hadoop]]
- [[Delta Lake]]
- [[Apache Iceberg]]

## 参考

- [Amazon S3 公式](https://aws.amazon.com/s3/)
- [Amazon S3 の特徴 - AWS](https://aws.amazon.com/s3/features/)
- [Object Storage Classes - Amazon S3](https://aws.amazon.com/s3/storage-classes/)
