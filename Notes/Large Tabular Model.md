---
title: "Large Tabular Model"
date: 2026-02-09
tags:
  - AI
  - ML
related:
  - "[[データフォーマット]]"
---

## 概要

Large Tabular Model（LTM）は、表形式（テーブル）データに特化した基盤モデル（Foundation Model）の総称である。LLM（Large Language Model）がテキストデータを扱うのに対し、LTM はスプレッドシートやデータベースのような構造化データから予測・分析を行う。2026年2月に Fundamental 社が NEXUS を公開し、エンタープライズ向け LTM として大きな注目を集めている。

## 詳細

### 背景：なぜ LTM が必要か

企業が扱うデータの大部分は構造化された表形式データであるが、従来の LLM は以下の点で表形式データの処理に限界がある:

- Transformer ベースのモデルはコンテキストウィンドウに制約があり、数十億行の大規模データセットの推論が困難
- テキストの逐次的なパターンに最適化されており、行と列にまたがる非線形な関係性の把握が苦手
- 従来の機械学習手法（XGBoost、Random Forest など）は手動での特徴量エンジニアリングが必要

LTM はこれらの課題を解決するため、表形式データの構造・パターン・依存関係を事前学習によって理解し、新しいデータセットに対して迅速に予測を行えるよう設計されている。

### Fundamental 社と NEXUS

#### 会社概要

Fundamental は2024年10月に設立された AI スタートアップで、CEO は Jeremy Fraenkel。2026年2月にステルスモードから脱し、Series A で2億5,500万ドルを調達（評価額12億ドル）。Seed ラウンド（3,000万ドル）と合わせて総額2億5,500万ドルの資金を確保した。

主な投資家:

- **リード**: Oak HC/FT
- **参加**: Valor Equity Partners、Battery Ventures、Salesforce Ventures、Hetz Ventures
- **エンジェル**: Perplexity CEO Aravind Srinivas、Wiz CEO Assaf Rappaport、Brex 共同創業者 Henrique Dubugras、Datadog CEO Olivier Pomel

#### NEXUS の特徴

NEXUS は Fundamental 社が開発した LTM であり、以下の特徴を持つ:

| 特徴 | 詳細 |
|------|------|
| **アーキテクチャ** | Transformer を使用せず、表形式データに最適化した独自アーキテクチャ |
| **決定論的** | 同じ入力に対して常に同じ結果を返す（LLM のような確率的な揺らぎがない） |
| **学習データ** | 数十億の実世界の表形式データセットで事前学習（Amazon SageMaker HyperPod 上） |
| **特徴量エンジニアリング不要** | 生のテーブルデータを直接取り込み、構造・パターン・依存関係を自動学習 |

#### ユースケース

Fortune 100 企業との7桁（数百万ドル）規模の契約を締結しており、以下の用途で活用されている:

- 需要予測
- 価格予測
- 顧客離反予測

#### AWS との提携

AWS とのパートナーシップにより、AWS ダッシュボード上でファーストパーティの「販売者」として登録されている。AWS 顧客は1行のコードで NEXUS をデプロイでき、通常数カ月かかる調達プロセスを省略可能。

### 他の主要な表形式基盤モデル

#### TabPFN（Prior Labs）

- **概要**: フライブルク大学発のスタートアップ Prior Labs が開発した表形式基盤モデル
- **発表**: 2024年に Nature 誌に掲載（「Accurate predictions on small data with a tabular foundation model」）
- **特徴**: 1億3,000万のデータセットで事前学習。10,000サンプル以下のデータセットで従来手法を大幅に上回る精度を達成
- **速度**: 2.8秒の推論で、4時間チューニングしたアンサンブルベースラインを超える性能
- **最新版**: TabPFN v2.5（2025年11月リリース）。最大50,000サンプル・2,000特徴量に対応

#### TabICL

- 最大60,000サンプルの合成データセットで事前学習された分類用の表形式基盤モデル
- 手頃な計算リソースで500,000サンプルの処理が可能

### 従来手法との比較

| 観点 | LTM（NEXUS 等） | XGBoost / Random Forest | LLM |
|------|----------------|------------------------|-----|
| データ型 | 表形式 | 表形式 | テキスト |
| 特徴量エンジニアリング | 不要 | 必要 | 不要 |
| 大規模データ対応 | 数十億行 | 中規模まで | コンテキスト制約 |
| 決定論性 | あり（NEXUS） | あり | なし |
| 事前学習 | あり | なし | あり |
| 転移学習 | 可能 | 不可 | 可能 |

## ポイント

- LTM は表形式データに特化した基盤モデルで、LLM が苦手とする構造化データの予測・分析を担う
- Fundamental 社は2024年10月設立、2026年2月に2億5,500万ドル調達で注目を集めた AI スタートアップ
- NEXUS は Transformer を使わない独自アーキテクチャで、決定論的な予測を実現
- TabPFN（Prior Labs）は小規模データセットでの高精度予測に強みを持ち、Nature に掲載された学術系モデル
- エンタープライズでの需要予測・価格予測・顧客離反予測が主要ユースケース
- 手動の特徴量エンジニアリングが不要になる点が従来の機械学習手法に対する大きな利点

## 関連項目

- [[データフォーマット]] - LTM が処理する表形式データの格納フォーマット

## 参考

- [Fundamental 公式ローンチ記事](https://fundamental.tech/news/launch)
- [Fundamental プレスリリース（BusinessWire）](https://www.businesswire.com/news/home/20260205966141/en/Fundamental-Announces-$255M-in-Funding-and-Publicly-Launches-its-Most-Powerful-Large-Tabular-Model-LTM)
- [TechCrunch - Fundamental raises $255M Series A](https://techcrunch.com/2026/02/05/fundamental-raises-255-million-series-a-with-a-new-take-on-big-data-analysis/)
- [Nature - Accurate predictions on small data with a tabular foundation model（TabPFN）](https://www.nature.com/articles/s41586-024-08328-6)
- [Prior Labs - TabPFN](https://priorlabs.ai/tabpfn)
- [arXiv - Why Tabular Foundation Models Should Be a Research Priority](https://arxiv.org/html/2405.01147v1)
