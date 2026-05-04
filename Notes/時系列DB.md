---
title: "時系列DB"
date: 2026-05-05
tags:
  - DB
  - NoSQL
related:
  - "[[NoSQL]]"
  - "[[ポリグロットデータストア]]"
  - "[[リアルタイム処理]]"
  - "[[OLAP]]"
  - "[[ポリグロット永続化]]"
---

## 概要

時系列DB（Time Series Database / TSDB）は，タイムスタンプ付きの計測値（メトリクス）を効率的に格納・圧縮・集計するために特化したデータベースである．IoT センサー・システム監視・金融相場・アプリケーションメトリクスなど，時刻と値のペアが連続的に発生するデータに対して，RDBMS より桁違いの書き込み性能と圧縮率を実現する．

## 詳細

### データ構造の特徴

時系列データは「タイムスタンプ × 指標名 × 値 × タグ（ラベル）」で構成される：

```
timestamp            metric          value   tags
2026-05-05T09:00:00  cpu_usage       72.3    host=server1, region=tokyo
2026-05-05T09:00:10  cpu_usage       74.1    host=server1, region=tokyo
2026-05-05T09:00:20  memory_usage    4096    host=server1, region=tokyo
2026-05-05T09:00:00  cpu_usage       45.2    host=server2, region=osaka
```

### RDBMS との比較

| 観点 | 時系列DB | RDBMS |
|------|---------|-------|
| 書き込み性能 | 超高速（100万points/sec 以上も） | 中程度 |
| 圧縮率 | 90%以上の圧縮が可能（デルタ圧縮等） | 低い |
| 時間範囲クエリ | 高速（時間インデックス最適化） | 遅い |
| データ保持（Retention） | 自動 Downsampling・削除ポリシー | 手動管理 |
| 一般的なクエリ | 時刻範囲・集計・補間 | 任意 SQL |
| JOIN | 限定的 | 完全対応 |

### 主要製品

| 製品 | 特徴 |
|------|------|
| **InfluxDB** | 最も普及した OSS 時系列 DB．Flux クエリ言語．InfluxDB 3.0（2025）はクラウドネイティブ設計 |
| **Prometheus** | オープンソースの監視・時系列 DB．Kubernetes 監視の標準 |
| **TimescaleDB** | PostgreSQL 拡張で時系列を実現．SQL がそのまま使えるのが強み |
| **Amazon Timestream** | AWS フルマネージド時系列 DB |
| **OpenTSDB** | HBase 上の時系列 DB（レガシー） |
| **VictoriaMetrics** | Prometheus 互換の高性能時系列 DB（Prometheus の代替として注目） |

### 代表的なアーキテクチャパターン

**システム監視スタック（2026の標準）:**

```
アプリ / インフラ
    ↓ メトリクス収集
Prometheus または VictoriaMetrics（スクレイプ）
    ↓ 保存
時系列 DB（Prometheus / VictoriaMetrics / Thanos）
    ↓ 可視化
Grafana（ダッシュボード）
    ↓ アラート
Alertmanager / PagerDuty
```

**IoT データパイプライン:**

```
IoT デバイス群
    ↓ MQTT / HTTP
メッセージブローカー（Kafka / MQTT Broker）
    ↓ リアルタイム処理
InfluxDB / Amazon Timestream（時系列蓄積）
    ↓
分析ダッシュボード / ML モデル
```

### ユースケース

- **システム監視**: CPU・メモリ・レイテンシ等のインフラメトリクス（Prometheus + Grafana が定番）
- **IoT センサー**: 温度・湿度・振動等の計測値の高頻度収集
- **金融市場データ**: 株価・為替レートの秒単位・ティックデータ
- **アプリケーション APM**: リクエスト数・エラー率・レスポンスタイムのトレンド

## ポイント

- タイムスタンプが最優先の索引となる「時間ファーストの設計」が時系列DBの核心
- デルタ圧縮・Gorilla 圧縮等の専用圧縮アルゴリズムで RDBMS より大幅にストレージを削減
- Retention Policy（保持ポリシー）と Downsampling（古いデータを間引く）が運用の重要機能
- [[ポリグロット永続化]] では監視・IoT 領域の担当として時系列DBを採用するのが典型パターン
- Prometheus が Kubernetes 環境で事実上の標準，Grafana と組み合わせての可視化が定番

## 関連項目

- [[NoSQL]] — 時系列DB が属するデータベースカテゴリ
- [[ポリグロットデータストア]] — 時系列DBを含む多様なデータストアの組み合わせ
- [[リアルタイム処理]] — 時系列DBがリアルタイムデータの蓄積先として連携
- [[OLAP]] — 時系列分析は OLAP の一領域として位置づけられる
- [[ポリグロット永続化]] — 監視・IoT 領域に時系列DBを使い分ける設計パターン

## 参考

- [時系列データベースとは？基本と活用のメリット | SINT](https://products.sint.co.jp/siob/blog/time-series-database)
- [時系列データベース - Wikipedia](https://ja.wikipedia.org/wiki/%E6%99%82%E7%B3%BB%E5%88%97%E3%83%87%E3%83%BC%E3%82%BF%E3%83%99%E3%83%BC%E3%82%B9)
- [Time Series Database - Amazon Timestream | AWS](https://aws.amazon.com/timestream/)
- [TimescaleDB 入門セミナー | SRA OSS](https://www.sraoss.co.jp/wp-content/uploads/files/event_seminar/material/2021/timescaledb-intro-20210624.pdf)
