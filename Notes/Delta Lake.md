---
title: "Delta Lake"
date: 2026-02-20
tags:
  - DB
  - Cloud
related:
  - "[[データレイク]]"
  - "[[データレイクハウス]]"
  - "[[Apache Iceberg]]"
  - "[[Apache Parquet]]"
  - "[[Apache Spark]]"
---

## 概要

Delta Lake は、データレイク上で ACID トランザクションとスケーラブルなメタデータ管理を実現するオープンソースのストレージフレームワークである。Apache Parquet ファイルをベースに、ファイルベースのトランザクションログ（DeltaLog）を加えることで、従来のデータレイクにデータウェアハウス的な信頼性をもたらし、Lakehouse アーキテクチャの基盤として広く利用されている。

## 詳細

### アーキテクチャ

#### ストレージ層

- **Apache Parquet**: カラムナー形式のストレージ。効率的なクエリ・圧縮・スキーマ進化をサポート
- **DeltaLog（トランザクションログ）**: すべての変更履歴を記録する JSON/Parquet のログファイル。テーブルの現在状態・過去の状態をすべて追跡する

#### データ操作フロー

```
[書き込み操作]
    ↓
[新しい Parquet ファイル生成]
    ↓
[DeltaLog に原子的にコミット]
    ↓
[並行リーダーには整合性のある状態を提供]
```

### 主要機能

#### ACID トランザクション

S3・ADLS・GCS・HDFS などの既存データレイク上で ACID（原子性・一貫性・独立性・永続性）を保証する。複数の並行書き込みが競合しても整合性が担保される。

#### スキーマ管理

- **スキーマ強制（Schema Enforcement）**: 不正なレコードのインジェストを防ぐ自動スキーマ検証
- **スキーマ進化（Schema Evolution）**: カラムの追加・削除・変更を既存データに影響なく行える

#### バッチ・ストリーミング統合

Delta テーブルはバッチテーブルであると同時にストリーミングソース・シンクとして機能する。ストリーミングデータの取り込み・バッチ履歴のバックフィル・インタラクティブクエリをすべて同一テーブルで実現する。

#### タイムトラベル（Time Travel）

DeltaLog によって過去のバージョンのデータにアクセスできる：

```sql
-- 特定バージョンのデータを参照
SELECT * FROM my_table VERSION AS OF 10;

-- タイムスタンプ指定での参照
SELECT * FROM my_table TIMESTAMP AS OF '2026-01-01';
```

#### DML 操作サポート

通常の Parquet ファイルと異なり、Delta Lake は UPDATE・DELETE・MERGE 操作をネイティブにサポートする。

### Delta UniForm（Universal Format）

Delta Lake 3.0 以降で導入された UniForm は、単一の Parquet ファイルコピーを維持しながら、Delta・Apache Iceberg・Apache Hudi 形式のメタデータを同時生成する機能。

```sql
-- UniForm を有効化（Iceberg 互換メタデータを追加生成）
ALTER TABLE my_table SET TBLPROPERTIES (
  'delta.universalFormat.enabledFormats' = 'iceberg'
);
```

書き込みのオーバーヘッドはほぼなく、Iceberg クライアントが Delta テーブルをそのまま読み取れる。

### Delta Lake 4.0（2025年リリース）

主要な新機能：
- **生成カラム（Generated Columns）**: 他カラムの式から値を自動導出するカラム
- **ネイティブカタログ統合**: カタログとの統合強化
- **半構造化データサポート強化**: JSON 等の半構造化データの扱いが向上
- **変更データキャプチャ（CDC）改善**: より詳細なメトリクス（ファイル数・テーブルサイズ・データ分布ヒストグラム）

### エコシステム連携

Delta Lake は以下のエンジンと連携可能：

- **計算エンジン**: Apache Spark・Apache Flink・Trino・PrestoDB・Apache Hive
- **クラウドサービス**: AWS Athena・Azure Synapse Analytics・Google BigQuery
- **BI ツール**: Databricks SQL・各種 JDBC/ODBC 対応ツール

### Apache Iceberg との比較

| 観点 | Delta Lake | Apache Iceberg |
|------|-----------|---------------|
| 開発元 | Databricks（OSS） | Netflix 発、Apache 管理 |
| 主な利用環境 | Databricks 中心 | マルチエンジン・マルチクラウド |
| ロック回避 | 楽観的同時実行制御 | 楽観的同時実行制御 |
| 互換性 | UniForm で Iceberg 対応 | Delta 読み取りはカスタムコネクタ必要 |
| カタログ | Unity Catalog 等 | REST Catalog・Hive Catalog 等 |

## ポイント

- Delta Lake は Databricks が中心的に開発しており、Databricks 環境ではネイティブサポート・最高のパフォーマンスが得られる
- UniForm により他フォーマットとの相互運用性が大幅に向上し、フォーマット選択の自由度が増した
- DeltaLog がすべての操作を記録するため、データのデバッグ・監査・ロールバックが容易
- ストリーミングと バッチを統一するため、Lambda アーキテクチャの複雑さを排除できる

## 関連項目

- [[データレイク]]
- [[データレイクハウス]]
- [[Apache Iceberg]]
- [[Apache Parquet]]
- [[Apache Spark]]

## 参考

- [Delta Lake 公式サイト](https://delta.io/)
- [Delta Lake 4.0 リリースノート](https://delta.io/blog/2025-09-25-delta-lake-40/)
- [Delta Lake UniForm - Databricks Blog](https://www.databricks.com/blog/delta-lake-universal-format-uniform-iceberg-compatibility-now-ga)
- [What is Delta Lake in Databricks?](https://docs.databricks.com/aws/en/delta/)
