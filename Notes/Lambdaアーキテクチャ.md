---
title: "Lambdaアーキテクチャ"
date: 2026-05-03
tags:
  - DB
  - Cloud
related:
  - "[[データアーキテクチャ]]"
  - "[[Kappaアーキテクチャ]]"
  - "[[Apache Kafka]]"
  - "[[Apache Spark]]"
  - "[[メダリオンアーキテクチャ]]"
---

## 概要

Lambda アーキテクチャは Nathan Marz が 2011 年に提唱した大規模データ処理のアーキテクチャパターン．バッチ処理（高精度・高スループット）とストリーム処理（低レイテンシ）を並列に実行し，サービング層でマージすることで「速度と正確性の両立」を図る．2010 年代のビッグデータ時代に広く採用されたが，運用複雑性の高さから [[Kappaアーキテクチャ]] への移行が進んでいる．

## 詳細

### 3層構造

```
データソース
     ↓（生データを2系統に分岐）
 ┌───────────┬────────────────┐
 │ バッチ層  │  スピード層    │
 │（高精度）  │ （低遅延）     │
 │           │                │
 │ 全履歴を  │ 直近データを   │
 │ 再計算    │ ストリーム処理 │
 └─────┬─────┴────────┬───────┘
       ↓               ↓
 バッチビュー    リアルタイムビュー
       └───────┬────────┘
           サービング層
           （クエリに応答）
```

#### バッチ層（Batch Layer）

- 全履歴データを再計算して「バッチビュー」を定期的に更新
- 高スループット・高精度だが遅延が大きい（数時間〜数日）
- 実装: Apache Hadoop / Apache Spark
- ストレージ: HDFS / S3 / GCS

#### スピード層（Speed Layer）

- 最新データのみをストリーム処理し「リアルタイムビュー」を提供
- 低遅延（秒〜ミリ秒）だが，完全な正確性は保証しない
- バッチ層の遅延を補完する役割
- 実装: Apache Kafka + Apache Storm / Apache Flink / Apache Spark Streaming

#### サービング層（Serving Layer）

- バッチビューとリアルタイムビューをマージしてクエリに応答
- ユーザーはこの層のみにアクセス
- 実装: Apache Cassandra / Apache HBase / Redis / Apache Hive

### 利点と欠点

| 観点 | 説明 |
|------|------|
| **精度** | バッチ層が全データを再計算するため高い精度を保証 |
| **耐障害性** | バッチ層で必ず修正できるため，スピード層のエラーが永続しない |
| **低遅延** | スピード層で直近データを即座に提供 |
| **複雑性** | 同じロジックをバッチ・ストリーム2系統で実装・維持する必要がある |
| **コード重複** | バッチとストリームで同一のビジネスロジックを二重管理するリスク |
| **スケールコスト** | 2系統のパイプラインを維持するインフラコストが高い |

### Lambda アーキテクチャの現状

2026 年現在，Lambda アーキテクチャは「複雑性の代名詞」として批判的に評価されることも多い．Jay Kreps（Apache Kafka 共同開発者）が 2014 年に提唱した [[Kappaアーキテクチャ]] は「バッチ層を除去して全てストリームで処理する」というシンプルな代替案で，採用が増加している．

ただし以下のケースでは Lambda の複雑性を受け入れる価値がある:
- 再集計コストが非常に高く，ストリーム処理だけでは賄えない場合
- バッチ処理による高精度な履歴分析が必要な場合
- 既存のバッチパイプラインを段階的にリアルタイム化する移行期間

## ポイント

- バッチ（精度）+ ストリーム（速度）の2系統を並走させ，サービング層でマージする構成
- 「速度と正確性の両立」が設計目標だが，2系統のロジック管理という複雑性が代償
- Apache Hadoop/Spark（バッチ）+ Apache Kafka/Flink（ストリーム）が定番スタック
- [[Kappaアーキテクチャ]] はバッチ層を廃止してストリームに一本化するシンプルな代替
- 2026 年現在は Kappa 寄りのストリーミングファースト設計への移行が加速している

## 関連項目

- [[データアーキテクチャ]] — Lambda はデータ処理パターンの一つ
- [[Kappaアーキテクチャ]] — Lambda のバッチ層を廃止したシンプルな代替パターン
- [[Apache Kafka]] — スピード層のメッセージングプラットフォーム
- [[Apache Spark]] — バッチ層・スピード層の両方で使用できる処理エンジン
- [[メダリオンアーキテクチャ]] — Lambda/Kappa と組み合わせ可能なデータ品質パターン

## 参考

- [Lambda architecture - Wikipedia](https://en.wikipedia.org/wiki/Lambda_architecture)
- [What is Lambda Architecture? - Databricks](https://www.databricks.com/blog/what-is-lambda-architecture)
- [Lambda Architecture 101 - Chaos Genius](https://www.chaosgenius.io/blog/lambda-architecture/)
- [Lambda Architecture on AWS with Spark - AWS Whitepaper](https://d1.awsstatic.com/whitepapers/lambda-architecure-on-for-batch-aws.d58fd1b7e263e8d378fca4e1e419b7eba48d6df3.pdf)
