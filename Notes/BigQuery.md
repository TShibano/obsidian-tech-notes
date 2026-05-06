---
title: "BigQuery"
date: 2026-05-06
tags:
  - Cloud
  - GCP
  - DB
related:
  - "[[Snowflake]]"
  - "[[Databricks]]"
  - "[[OLAP]]"
  - "[[データウェアハウス]]"
  - "[[SQL]]"
  - "[[クラウドデータOS]]"
---

## 概要

BigQueryはGoogle Cloudが提供するフルマネージド型のクラウドデータウェアハウス（DWH）．サーバーレスアーキテクチャとカラム型ストレージ・分散クエリエンジンにより，ペタバイト規模のデータを高速にSQLで分析できる．

## 詳細

### アーキテクチャ

BigQueryはコンピューティングとストレージを分離した設計を採用:

```
クライアント
    ↓ SQL クエリ
ルートサーバー（Dremel ベース）
    ↓ クエリを分解・配布
ミキサーサーバー（中間集計）
    ↓
リーフサーバー群（実際のデータ読み込み・処理）
    ↓
Colossus（Google 分散ストレージ）
```

| コンポーネント | 役割 |
|-------------|------|
| Dremel | ツリー構造でクエリを並列分解・実行するエンジン |
| Colossus | Google の分散ファイルシステム（ストレージ層） |
| Borg | Google のクラスタ管理システム |
| Jupiter | Google 内部ネットワーク（高帯域） |

### カラム型ストレージ

BigQueryはカラム型（列指向）フォーマットでデータを格納:
- 分析クエリで必要な列だけを読み込むため I/O が最小化
- 同じ型のデータをまとめて圧縮するため圧縮率が高い

### 料金モデル

| モデル | 内容 | 向いている場合 |
|--------|------|--------------|
| オンデマンド | スキャンしたデータ量に応じた課金 | クエリ頻度が低い |
| 定額（Capacity） | スロット数（処理単位）で固定料金 | 大量クエリを頻繁に実行 |

### 主な機能

- **BigQuery ML**: SQL でモデル学習・推論が可能
- **BigQuery Omni**: AWS/Azure 上のデータを BigQuery から直接クエリ
- **BigLake**: オブジェクトストレージ（GCS）をテーブルとして扱う
- **ストリーミング挿入**: リアルタイムデータ取り込み

## ポイント

- サーバーレスのため，インフラ管理が不要（スケールは自動）
- SELECT の際にスキャンするデータ量で課金されるため，クエリ最適化（列・パーティション選択）が重要
- Snowflake との違い: BigQuery は GCP に紐づく，Snowflake はマルチクラウド対応

## 関連項目

- [[Snowflake]]
- [[Databricks]]
- [[OLAP]]
- [[データウェアハウス]]
- [[SQL]]
- [[クラウドデータOS]]

## 参考

- [BigQuery の概要 | Google Cloud Documentation](https://cloud.google.com/bigquery/docs/introduction?hl=ja)
- [BigQueryがなぜ人気なのかをアーキテクチャの観点から考えてみた！](https://techblog.nhn-techorus.com/archives/38649)
- [グーグルのBigQuery，高速処理の仕組みは「カラム型データストア」と「ツリー構造」](https://www.publickey1.jp/blog/12/bigquery_1.html)
