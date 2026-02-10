---
title: "Apache Avro"
date: 2026-02-07
tags:
  - Cloud
  - DB
related:
  - "[[Apache Parquet]]"
  - "[[データレイク]]"
  - "[[データ基盤]]"
  - "[[Apache Iceberg]]"
  - "[[Polars]]"
  - "[[データフォーマット]]"
  - "[[Apache Hive]]"
---

## 概要

Apache Avro は、Apache Software Foundation が開発したオープンソースの**行指向データシリアライゼーションフレームワーク**である。JSON でスキーマを定義し、データをコンパクトなバイナリ形式にシリアル化する。スキーマ進化（Schema Evolution）への強力なサポートが最大の特徴で、ストリーミングデータパイプラインやメッセージングシステム（Apache Kafka 等）のデファクトシリアライゼーション形式として広く採用されている。

## 詳細

### Avro の基本構造

Avro ファイル（Object Container File）は以下の構造を持つ:

| 構成要素 | 説明 |
|---------|------|
| **ヘッダー** | マジックバイト、ファイルメタデータ（スキーマ定義を含む）、同期マーカー |
| **データブロック** | シリアル化されたレコードの集合。ブロック単位で圧縮される |
| **同期マーカー** | ブロック間の区切り。ファイルの分割・並列処理に利用 |

データとスキーマが一緒に格納されるため、ファイルを受け取ったプログラムはスキーマを参照してデータを正しくデシリアル化できる。

### スキーマの定義

Avro スキーマは JSON で記述される:

```json
{
  "type": "record",
  "name": "User",
  "fields": [
    {"name": "name", "type": "string"},
    {"name": "age", "type": "int"},
    {"name": "email", "type": ["null", "string"], "default": null}
  ]
}
```

サポートされるデータ型:
- **プリミティブ型**: null, boolean, int, long, float, double, bytes, string
- **複合型**: record, enum, array, map, union, fixed

### スキーマ進化（Schema Evolution）

Avro の最大の強みであるスキーマ進化により、時間経過に伴うスキーマ変更を安全に行える:

| 変更タイプ | 説明 | 互換性 |
|-----------|------|--------|
| **フィールド追加** | デフォルト値付きで新しいフィールドを追加 | 後方互換（新しいコードが古いデータを読める） |
| **フィールド削除** | デフォルト値を持つフィールドを削除 | 前方互換（古いコードが新しいデータを読める） |
| **型の昇格** | int → long、float → double 等の型変更 | 後方互換 |
| **フィールド名変更** | エイリアスを使用してフィールド名を変更 | 後方互換 |

互換性レベル:

| レベル | 説明 |
|--------|------|
| **BACKWARD** | 新しいスキーマで古いデータを読める |
| **FORWARD** | 古いスキーマで新しいデータを読める |
| **FULL** | BACKWARD + FORWARD の両方を満たす |
| **NONE** | 互換性チェックなし |

### メリット

- **コンパクトなバイナリ形式**: フィールド名やタグをデータに含めないため、Protocol Buffers や JSON に比べてデータサイズが小さい
- **スキーマ進化**: 後方互換・前方互換・完全互換をサポートし、スキーマの変更に安全に対応
- **スキーマの自己記述性**: データとスキーマが一緒に格納されるため、任意のプログラムでデータを処理可能
- **多言語対応**: Java、Python、C/C++/C#、Ruby、Rust、JavaScript、PHP 等の実装が提供
- **RPC サポート**: データシリアライゼーションだけでなく、RPC プロトコルの定義にも利用可能
- **Kafka との親和性**: Schema Registry と組み合わせることで、メッセージスキーマのバージョン管理と互換性チェックを自動化

### 効率化技術

#### 1. バイナリエンコーディング

Avro はスキーマ情報をデータ本体に含めず、コンパクトなバイナリ形式でシリアル化する。各レコードにはフィールド名やタグが含まれないため、JSON や Protocol Buffers よりもデータサイズが小さくなる。

#### 2. ブロック圧縮

データブロック単位で圧縮を適用可能:

| 圧縮コーデック | 特徴 |
|--------------|------|
| **null** | 圧縮なし |
| **Deflate** | 標準的な圧縮。広い互換性 |
| **Snappy** | 高速な圧縮・解凍。ストリーミング向き |
| **ZSTD** | 高い圧縮率と高速な処理のバランス |
| **Bzip2** | 最も高い圧縮率。低速 |

#### 3. スキーマの分離（Schema Registry）

Confluent Schema Registry を使用して、スキーマをデータから分離して管理する:

- データにはスキーマ ID のみを含め、スキーマ本体は Schema Registry に格納
- ストリーミングデータの各レコードにスキーマを含める必要がなく、オーバーヘッドを削減
- スキーマの互換性チェックを自動化し、破壊的変更を防止

#### 4. ソート順の指定

スキーマでフィールドのソート順（ascending / descending / ignore）を指定でき、データの並び替えを効率化。MapReduce や分散処理でのキー比較に活用される。

### Apache Parquet との比較

| 観点 | Apache Avro | [[Apache Parquet]] |
|------|------------|-------------------|
| **データ配置** | 行指向（Row-based） | 列指向（Columnar） |
| **主な用途** | データシリアライゼーション、ストリーミング、メッセージング | 分析クエリ、データウェアハウス、OLAP |
| **圧縮効率** | 良好（バイナリ形式） | 非常に高い（同一型の列をまとめて圧縮） |
| **全レコード処理** | 高速（行単位でまとめて読める） | やや低速 |
| **特定列の分析** | 低速（全列を読む必要あり） | 高速（必要な列のみ読める） |
| **スキーマ進化** | 非常に強力 | サポートあり（Avro ほど柔軟ではない） |
| **書き込み性能** | 高速 | やや低速（列変換のオーバーヘッド） |
| **適するパターン** | Write-heavy、ストリーミング | Read-heavy、分析 |

### 最新動向（2025〜2026年）

- **Apache Avro 1.12.1**（2025年10月リリース）: セキュリティ修正4件を含むリリース
- **Kafka 4.0 との統合**: Kafka 4.0 のリリースに伴い、Avro シリアライゼーションの互換性に関する更新が行われた
- **Schema Registry の進化**: 単なる Avro スキーマストアから包括的なデータガバナンスプラットフォームへ進化。Avro 以外に JSON Schema、Protobuf もサポート
- [[Apache Iceberg]] のメタデータフォーマットとして Avro が採用されている

## ポイント

- 行指向のバイナリシリアライゼーション形式で、ストリーミングとメッセージングに最適
- スキーマ進化が最大の強み。後方互換・前方互換・完全互換を柔軟にサポート
- Apache Kafka + Schema Registry との組み合わせがデファクトスタンダード
- 分析クエリには[[Apache Parquet]]が適し、データの書き込み・ストリーミングには Avro が適する（相互補完の関係）
- [[Apache Iceberg]] のメタデータフォーマットとしても採用されている

## 関連項目

- [[Apache Parquet]]
- [[データレイク]]
- [[データ基盤]]
- [[Apache Iceberg]]
- [[Polars]] - Avro をサポートする高性能 DataFrame ライブラリ
- [[データフォーマット]] - Avro を含むデータフォーマットの一覧と比較
- [[Apache Hive]] - Avro を標準対応する SQL 分析基盤

## 参考

- [Apache Avro 公式サイト](https://avro.apache.org/)
- [IBM - What Is Apache Avro?](https://www.ibm.com/think/topics/avro)
- [Confluent - スキーマ進化と互換性](https://docs.confluent.io/ja-jp/platform/7.1/schema-registry/avro.html)
- [メルカリエンジニアリング - Apache Avro に入門した](https://engineering.mercari.com/blog/entry/2019-05-20-115839/)
- [Airbyte - Parquet vs. Avro](https://airbyte.com/data-engineering-resources/parquet-vs-avro)
- [Cloudurable - Kafka, Avro Serialization and the Schema Registry 2025](https://cloudurable.com/blog/kafka-avro-schema-registry-2025/)
