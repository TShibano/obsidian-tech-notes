---
title: "Polars"
date: 2026-02-07
tags:
  - Language
  - Python
  - Rust
related:
  - "[[Rust]]"
  - "[[Apache Parquet]]"
  - "[[DuckDB]]"
  - "[[Apache Avro]]"
---

## 概要

Polars は [[Rust]] で実装された高性能な DataFrame ライブラリで、Apache Arrow のメモリモデルを基盤としている。Python、R、Node.js から利用可能で、遅延評価（Lazy Evaluation）とマルチコア並列処理により、pandas と比較して3〜10倍以上のパフォーマンスを実現する。

## 詳細

### 背景と歴史

Polars は 2020 年に Ritchie Vink によって開発が開始された。従来の DataFrame ワークフロー（特に pandas）のパフォーマンスとエルゴノミクスの課題を解決することを目的としている。MIT ライセンスで公開され、約2週間ごとに新バージョンがリリースされる高速なリリースサイクルを持つ。2026年2月時点のバージョンは v1.38 系。

### コアアーキテクチャ

#### Apache Arrow ベースの列指向ストレージ

Polars はデータを行単位ではなく列単位で格納する。Apache Arrow のメモリフォーマットを採用することで:

- 必要な列のみを読み取るため、ディスク I/O とメモリアクセスを最小化
- [[Apache Parquet]] 等の列指向フォーマットとのゼロコピー連携が可能
- SIMD（Single Instruction, Multiple Data）命令による高速な演算

#### マルチコア並列処理

Rust の並行性モデルを活用し、すべての CPU コアを使用して処理を実行する。pandas がシングルスレッドで動作するのに対し、Polars は自動的に処理を並列化する。

#### 遅延評価（Lazy Evaluation）

Polars の最も特徴的な機能の一つ。LazyFrame を使用すると、クエリオプティマイザが以下の最適化を自動的に適用する:

| 最適化 | 説明 |
|--------|------|
| **述語プッシュダウン（Predicate Pushdown）** | フィルタ条件をデータ読み取り時点で適用し、不要なデータを早期に除外 |
| **射影プッシュダウン（Projection Pushdown）** | 必要な列のみを読み取り、メモリ使用量を削減 |
| **クエリの再編成** | 実行順序を最適化し、冗長な計算を排除 |
| **ストリーミング処理** | `streaming=True` で RAM を超えるデータセットを処理可能 |

### 主な特徴

| 特徴 | 説明 |
|------|------|
| **即時評価と遅延評価の両対応** | DataFrame（即時）と LazyFrame（遅延）の両方を提供 |
| **表現力豊かな API** | メソッドチェーンによる直感的なデータ変換 |
| **多様な入出力** | CSV、[[Apache Parquet]]、JSON、[[Apache Avro]]、Excel、データベース等に対応 |
| **ウィンドウ関数** | SQL ライクなウィンドウ関数を式で表現可能 |
| **UDF 拡張** | Rust でコンパイルした UDF（ユーザー定義関数）で拡張可能 |
| **メモリ効率** | Arrow 形式の列指向ストレージにより、pandas と比較してメモリ使用量が大幅に低い |

### pandas との比較

| 観点 | Polars | pandas |
|------|--------|--------|
| 実装言語 | [[Rust]] | C / Cython |
| メモリモデル | Apache Arrow（列指向） | NumPy（行指向寄り） |
| 並列処理 | マルチコア自動並列化 | シングルスレッド |
| 遅延評価 | LazyFrame で対応 | なし |
| 大規模データ | ストリーミングで RAM 超も可能 | RAM に収まるデータのみ |
| パフォーマンス | 一般的に3〜10倍高速、最大30倍以上 | 基準 |
| API | 式ベース（Expression API） | インデックスベース |
| エコシステム | 急速に成長中 | 非常に成熟（scikit-learn 等と統合） |

### ユースケース

- **大規模 CSV/Parquet の処理**: ファイルベースのデータ処理で pandas から移行するケースが多い
- **ETL パイプライン**: 遅延評価による最適化で効率的なデータ変換
- **探索的データ分析**: 高速な集計・結合により、大規模データでもインタラクティブな分析が可能
- **Spark の代替**: 分散クラスタが不要な中規模データ（数百 GB まで）では、Polars の方がシンプルかつ高速

### ベストプラクティス

- 複雑なパイプラインでは **LazyFrame** を使用し、クエリオプティマイザに最適化を委ねる
- データは **Parquet フォーマット**で保存し、列指向ストレージの恩恵を最大化する
- 中間 DataFrame の生成を避け、メソッドチェーンで処理を連結する
- `POLARS_MAX_THREADS` を設定し、利用可能なすべての CPU コアを活用する
- RAM を超えるデータには `streaming=True` オプションを使用する

### 最新動向（2026年）

- **v1.38 系**がリリース中。Float16 データ型の追加、リスト列間の算術演算サポートなどが追加
- Polars Cloud（商用サービス）の展開が進行中
- GPU アクセラレーション（cuDF 連携）のサポートが強化
- R バインディングの安定化が進行

## ポイント

- Rust + Apache Arrow ベースで、pandas と比較して3〜10倍の高速処理を実現
- 遅延評価（LazyFrame）により、クエリオプティマイザが自動的に最適な実行計画を生成
- マルチコア並列処理が標準で、シングルスレッドの pandas とは根本的に異なるアーキテクチャ
- ストリーミング処理により RAM を超えるデータセットも処理可能
- 分散クラスタ不要の中規模データでは Spark の代替として有力

## 関連項目

- [[Rust]] - Polars の実装言語
- [[Apache Parquet]] - Polars と相性が良い列指向ファイルフォーマット
- [[Apache Avro]] - Polars がサポートするデータフォーマット
- [[DuckDB]] - 同じく高速な分析エンジン。SQL ベースのアプローチ。相互連携が可能

## 参考

- [Polars 公式サイト](https://pola.rs/)
- [Polars ドキュメント](https://docs.pola.rs/)
- [GitHub - pola-rs/polars](https://github.com/pola-rs/polars)
- [JetBrains - Polars vs. pandas: What's the Difference?](https://blog.jetbrains.com/pycharm/2024/07/polars-vs-pandas/)
- [Databricks - Polars vs Pandas](https://www.databricks.com/glossary/polaris-vs-pandas)
- [Polars Case Study: Vydia](https://pola.rs/posts/case-vydia/)
