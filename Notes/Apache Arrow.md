---
title: "Apache Arrow"
date: 2026-02-09
tags:
  - Cloud
  - DB
related:
  - "[[Apache Parquet]]"
  - "[[DuckDB]]"
  - "[[Polars]]"
  - "[[データフォーマット]]"
  - "[[Apache Hadoop]]"
  - "[[データレイクハウス]]"
---

## 概要

Apache Arrow は、列指向のインメモリデータフォーマットとそれを扱う多言語ツールボックスを提供するオープンソースプロジェクトである。2016年に Apache Software Foundation から発表され、[[DuckDB]]、[[Polars]]、Spark など多くのデータ処理エンジンの基盤として採用されている。異なるシステム間でゼロコピーのデータ共有を可能にし、分析ワークロードの高速化に貢献している。

## 詳細

### 歴史

Apache Arrow は2016年2月17日に Apache Software Foundation から発表された。複数のオープンソースデータ分析プロジェクト（Drill、Impala、Kudu、Pandas、Spark 等）の開発者が共同で開発を主導した。各プロジェクトが独自のインメモリデータ表現を持つことによるシリアライゼーション/デシリアライゼーションのオーバーヘッドを排除することが主な動機であった。

### Arrow 列指向フォーマット

Arrow のコアは、言語非依存の列指向メモリフォーマット仕様である。

主な特徴:

- **O(1) ランダムアクセス**: 任意の配列インデックスへの定数時間アクセスが可能
- **キャッシュ効率**: 列指向レイアウトにより、分析ワークロードで高いキャッシュ効率を実現
- **SIMD 最適化**: 連続したメモリレイアウトにより、最新 CPU の SIMD（Single Instruction, Multiple Data）命令を活用可能
- **豊富なデータ型**: ネスト型やユーザー定義型を含む豊富なデータ型システムをサポート

#### 多言語実装

Arrow フォーマットは以下の言語で独立した実装を持ち、相互に統合テストされている:

C++、.NET、Go、Java、JavaScript、Julia、[[Rust]]、Swift、Python（PyArrow）

### Arrow IPC（Inter-Process Communication）

Arrow IPC は Arrow のインメモリフォーマットをそのままファイルやストリームとして永続化・転送する仕組みである。

- **ゼロコピー読み取り**: ディスク上の表現とメモリ上の表現が同一のため、メモリマッピングによりデシリアライゼーションコストがゼロ
- **ストリーミング**: データを1,000行単位のバッチとして逐次処理することが可能
- [[DuckDB]] は Arrow コミュニティ拡張を通じて Arrow IPC ファイルの読み書きをサポート

### Arrow Flight

Arrow Flight は Arrow データの高速ネットワーク転送を実現する RPC フレームワークである。

- gRPC 上に構築され、Arrow 列指向データのストリーミング転送に最適化
- **Flight SQL**: SQL クエリの実行結果を Arrow フォーマットで直接返すプロトコル
- 2025年以降、Flight SQL の採用が拡大し、レイクハウスエンジン間のインターオペラビリティ層として機能

### Apache Parquet との違い

Arrow と [[Apache Parquet]] はどちらも列指向だが、設計目的と使用場面が異なる:

| 観点 | Apache Arrow | [[Apache Parquet]] |
|------|-------------|-------------------|
| **目的** | インメモリ処理の効率化 | ディスク上のストレージ効率化 |
| **配置場所** | メモリ上 | ディスク上 |
| **圧縮** | 通常なし（CPU ネイティブレイアウト） | 高度な圧縮・エンコーディング（Snappy、Zstd 等） |
| **ランダムアクセス** | O(1) | 不可（チャンク単位のデコードが必要） |
| **読み取りコスト** | ゼロコピーでの読み取り可能 | デコード処理が必要 |
| **ファイルサイズ** | 大きい | 小さい（高圧縮） |
| **主な用途** | プロセス間データ転送、インメモリ分析 | データレイク、長期保存 |

**相互補完的な関係**: ディスク上のデータを Parquet で圧縮格納し、メモリに読み込む際に Arrow フォーマットに展開するのが一般的なパターンである。これにより、ストレージ効率と処理速度の両方を最大化できる。

### 主な採用事例

| プロジェクト | Arrow の利用方法 |
|------------|----------------|
| **[[Polars]]** | インメモリデータフレームのバックエンドとして Arrow を採用 |
| **[[DuckDB]]** | Arrow フォーマットでのデータ入出力、Polars との連携に Arrow を利用 |
| **Apache Spark** | PySpark で Arrow を利用した Pandas 連携の高速化 |
| **Dremio** | Arrow Flight を活用したデータレイクエンジン |
| **pandas** | PyArrow バックエンドによるメモリ効率と処理速度の改善 |

## ポイント

- Apache Arrow は列指向のインメモリデータフォーマットであり、ディスク上の格納形式である [[Apache Parquet]] とは相互補完的な関係
- ゼロコピー・O(1) ランダムアクセス・SIMD 最適化により、分析ワークロードを高速化
- 多言語（C++、Java、Python、[[Rust]] 等）で独立した実装を持ち、言語をまたいだデータ共有が可能
- Arrow IPC によりデシリアライゼーションコストゼロのファイル読み書きを実現
- Arrow Flight / Flight SQL がレイクハウスエンジン間のインターオペラビリティ層として普及拡大中
- [[DuckDB]]、[[Polars]]、Spark など主要な分析エンジンの基盤技術として広く採用

## 関連項目

- [[Apache Parquet]] - Arrow と補完関係にあるディスク上の列指向フォーマット
- [[DuckDB]] - Arrow コミュニティ拡張で Arrow IPC をサポートする分析エンジン
- [[Polars]] - Arrow をインメモリバックエンドとして採用するデータフレームライブラリ
- [[データフォーマット]] - Arrow を含むデータ形式の体系的な整理
- [[Apache Hadoop]] - Arrow が解決するプロセス間データ転送の課題の起源
- [[データレイクハウス]] - Arrow Flight がインターオペラビリティ層として機能するアーキテクチャ

## 参考

- [Apache Arrow 公式サイト](https://arrow.apache.org/)
- [Arrow Columnar Format 仕様](https://arrow.apache.org/docs/format/Columnar.html)
- [Apache Arrow FAQ](https://arrow.apache.org/faq/)
- [Apache Arrow GitHub](https://github.com/apache/arrow)
- [Arrow IPC Support in DuckDB](https://duckdb.org/2025/05/23/arrow-ipc-support-in-duckdb)
- [KDnuggets - Apache Arrow and Apache Parquet: Why We Needed Different Projects](https://www.kdnuggets.com/2017/02/apache-arrow-parquet-columnar-data.html)
