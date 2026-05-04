---
title: "Kappaアーキテクチャ"
date: 2026-05-03
tags:
  - DB
  - Cloud
related:
  - "[[データアーキテクチャ]]"
  - "[[Lambdaアーキテクチャ]]"
  - "[[Apache Kafka]]"
  - "[[メダリオンアーキテクチャ]]"
  - "[[リアルタイム処理]]"
---

## 概要

Kappa アーキテクチャは 2014 年に Jay Kreps（Apache Kafka 共同開発者，Confluent 創業者）が提唱したデータ処理アーキテクチャ．[[Lambdaアーキテクチャ]] のバッチ層を廃止し，**全てのデータをストリームとして処理**するシンプルな設計を採用する．ログベースのストレージ（Kafka 等）に全履歴を保持することで，バッチ再処理をストリーム処理のリプレイで代替する．データウェアハウスやデータレイクなど上で実現できる．

## 詳細

### 3つの原則
- リアルタイム処理
	- 受信と同時に処理が開始される
- 単一のイベントストリーム
	- すべてのデータを単一のイベントストリームに保存する
- ステートレス処理
	- 各イベントはそれ以前のイベントの状態に依存せず独立しているため，ノード間で同期が不要で容易にシステムを拡張できる

### 基本構造

```
データソース
     ↓
ストリームストレージ（Kafka など）
     ↓  ← ここに全履歴を保持（無限ログ）
ストリーム処理エンジン
（Apache Flink / Apache Kafka Streams）
     ↓
サービング層
（クエリに応答）
```

Lambda の3層（バッチ・スピード・サービング）を，ストリーム1本に統一する．

### Lambda との比較

| 項目 | [[Lambdaアーキテクチャ]] | Kappaアーキテクチャ |
|------|------------------|------------------|
| 処理系統 | バッチ + ストリームの2系統 | ストリーム1系統 |
| コード管理 | 2系統のロジックを並行維持 | 1つのコードベース |
| 再処理 | バッチで全データ再計算 | ストリームのリプレイ |
| レイテンシ | 秒〜分（スピード層）+ 時間〜日（バッチ）| 秒〜ミリ秒 |
| 精度 | バッチが補正するため高い | ストリーム処理の精度に依存 |
| 複雑性 | 高（2系統の設計・運用） | 低（1系統） |
| コスト | 高（2系統のインフラ） | 中（Kafka のリテンションコスト） |

### 再処理（リプレイ）の仕組み

Kappa の核心は「Kafka などのログに全履歴を保持し，再処理が必要なときはそのログをリプレイする」という考え方．

```
[再処理が必要になった場合]

Kafka (全履歴ログ)
  └─ 新バージョンのストリーム処理ジョブ を起動
     └─ 先頭 offset から読み直して新サービング層を構築
     └─ 完成後，古いサービング層と切り替え
```

### 定番スタック（2026）

Apache Kafka + Apache Flink + Apache Iceberg の組み合わせが Kappa 実装の標準スタックとして台頭．

```
イベントソース
    ↓
Apache Kafka（全履歴保持のストリームログ）
    ↓
Apache Flink（ステートフルストリーム処理）
    ↓
Apache Iceberg（オープンテーブルフォーマット・リプレイ対応）
    ↓
分析エンジン（Spark / Trino / DuckDB 等）
```

### 2026 年の位置づけ

Kappa はビッグデータ・AI エージェント・IoT などリアルタイム処理の需要拡大を背景に普及が加速．Uber・Shopify・Twitter（現 X）・Disney などが採用．

Agentic AI の台頭により「イベント駆動でリアルタイムにデータを処理・応答する」設計が重要性を増しており，Kappa のストリーミングファースト設計との親和性が高い．

### 弱点

- Kafka のデータリテンション（保持コスト）が高くなる可能性
- 長大な履歴のリプレイは時間がかかる（ただし並列化で対処可能）
- ストリーム処理エンジン（Flink 等）の習熟コスト

## ポイント

- [[Lambdaアーキテクチャ]] の「2系統の並行管理」という複雑性を，ストリーム1本に統一して解消
- Kafka 等のログに全履歴を保持し，再処理時はそこからリプレイする設計
- Apache Kafka + Apache Flink + Apache Iceberg が 2026 年の標準 Kappa スタック
- 2026 年現在，ストリーミングファースト設計への移行トレンドの中でデファクトに近い地位を確立
- Agentic AI・IoT・リアルタイム分析ニーズの高まりと設計思想が合致

## 関連項目

- [[データアーキテクチャ]] — Kappa はデータ処理パターンの一つ
- [[Lambdaアーキテクチャ]] — Kappa が解決しようとした先行パターン
- [[Apache Kafka]] — Kappa のストリームログの中核コンポーネント
- [[メダリオンアーキテクチャ]] — Kappa パイプラインと組み合わせ可能なデータ品質パターン
- [[リアルタイム処理]] — Kappa アーキテクチャが全処理を統一するストリーミング処理方式

## 参考

- [Kappa Architecture 101 - Chaos Genius](https://www.chaosgenius.io/blog/kappa-architecture/)
- [Kappa Architecture Overview - Hazelcast](https://hazelcast.com/foundations/software-architecture/kappa-architecture/)
- [The Rise of Kappa Architecture in the Era of Agentic AI - Kai Waehner](https://www.kai-waehner.de/blog/2025/07/08/the-rise-of-kappa-architecture-in-the-era-of-agentic-ai-and-data-streaming/)
- [Kappa vs Lambda Architecture - Flexera](https://www.flexera.com/blog/finops/kappa-vs-lambda-architecture/)
