---
title: "TPU"
date: 2026-02-18
tags:
  - AI
  - ML
  - Cloud
  - GCP
related:
  - "[[Gemini]]"
  - "[[MLOps]]"
  - "[[Large Tabular Model]]"
---

## 概要

TPU（Tensor Processing Unit）は Google が設計した AI・機械学習専用の ASIC（特定用途向け集積回路）である。行列乗算を高速に処理するシストリックアレイ構造を採用し、GPU と比べて電力効率に優れたML推論・学習向けハードウェアとして Google Cloud 上で提供されている。

## 詳細

### 背景と開発の動機

Google は AlphaGo や Deep Learning を大規模展開するにあたり、GPU では電力効率・スループット・コストの面で限界があると判断し、行列演算に特化したカスタムチップの開発を開始した。2015年ごろから社内利用を開始し、2017年に Google I/O で公開発表された。

### アーキテクチャ

#### TensorCore

TPU チップの基本計算単位。以下のサブユニットで構成される:

| サブユニット | 役割 |
|------------|------|
| **MXU（Matrix Multiply Unit）** | 行列乗算の主計算ユニット。シストリックアレイで構成 |
| **VPU（Vector Processing Unit）** | 活性化関数・正規化など要素単位の演算 |
| **スカラーユニット** | 制御フローなど汎用スカラー演算 |

#### MXU（Matrix Multiply Unit）

シストリックアレイ方式で大規模な行列積を実行する:

- **v5e 以前**: 128×128 の乗算累算器
- **v6e・v7**: 256×256 に拡大（1サイクルで 65,536 MAC 演算）
- 入力: bfloat16、累算: FP32

#### SparseCore

埋め込みテーブルなどスパース演算を高速化する専用プロセッサ:

- **v5p・v7**: チップ当たり 4 基
- **v6e**: チップ当たり 2 基
- 推薦システムのような大規模スパースモデルに有効

#### インターコネクト（ICI: Inter-Chip Interconnect）

TPU Pod 内のチップ間を高帯域幅で接続:

| バージョン | トポロジー |
|-----------|-----------|
| v2・v3・v5e・v6e | 2D トーラス（4近傍接続） |
| v4・v5p・v7 | 3D トーラス（6近傍接続）。通信ホップ数が大幅に減少 |

### 世代別サマリ

| 世代 | コード名 | 発表年 | 主な特徴 |
|------|---------|--------|---------|
| v1 | - | 2016 | Google 社内専用。推論特化 |
| v2 | - | 2017 | 学習対応。Cloud TPU として提供開始 |
| v3 | - | 2018 | 液冷・倍精度サポート |
| v4 | - | 2021 | 3D トーラス接続。大規模並列学習 |
| v5e | - | 2023 | コスト効率重視。GKE 対応 |
| v5p | - | 2023 | パフォーマンス重視。SparseCore 強化 |
| v6e | Trillium | 2024 | v5e 比 4.7 倍性能。HBM 容量・帯域倍増 |
| v7 | Ironwood | 2025 | 推論特化。42.5 Exaflops（9,216チップ時）|

### TPU v7（Ironwood）

2025年4月の Google Cloud Next で発表された第7世代 TPU。「推論の時代のための TPU」として設計されており、大規模言語モデルや推論重視の Thinking Model に最適化されている。

- **計算能力**: 最大 9,216 チップで **42.5 Exaflops**（世界最大スーパーコンピュータを超える規模）
- **設計思想**: 学習よりも推論ワークロードを優先。低遅延・高スループットの推論サービングに特化
- **[[Gemini]] との連携**: [[Gemini]] モデルの推論インフラとして活用

### TPU Pod と Slice

- **Pod**: 高速 ICI で接続された TPU チップ群の最大構成
- **Slice**: 1つの Pod 内で ICI によりメッシュ接続された部分集合
- **Multislice**: データセンターネットワーク（DCN）経由で複数 Slice をまたいだ大規模並列処理

### Cloud TPU の利用方法

Google Cloud 上で以下の形態で利用可能:

1. **TPU VM**: TPU ホスト上に直接 SSH 接続して利用
2. **GKE（Google Kubernetes Engine）**: Kubernetes ワークロードとして TPU を使用
3. **Vertex AI**: マネージドトレーニング・サービング環境

主な ML フレームワークとの対応:
- **JAX**: Google の数値計算ライブラリ。TPU との親和性が最も高い
- **TensorFlow / PyTorch**: XLA コンパイラ経由で対応

## ポイント

- シストリックアレイ（MXU）による行列乗算の専用設計で、GPU 比で電力効率が高い
- 世代を重ねるごとにトポロジー（2D → 3D トーラス）と MXU サイズ（128² → 256²）が進化
- v7（Ironwood）は推論特化設計で、[[Gemini]] などの大規模モデルサービングを担う
- Google Cloud で VM・GKE・Vertex AI から利用可能。JAX との組み合わせが最も効率的
- GPU（NVIDIA）と比べてソフトウェアエコシステムは狭いが、Google の自社モデル開発では中核的役割を担う

## 関連項目

- [[Gemini]] - Google の LLM。TPU v7（Ironwood）を推論インフラとして活用
- [[MLOps]] - 機械学習モデルの本番運用。Cloud TPU はトレーニング・サービング基盤として利用
- [[Large Tabular Model]] - 大規模表形式データのモデル。TPU での学習が有効なユースケース

## 参考

- [Tensor Processing Units (TPUs) - Google Cloud](https://cloud.google.com/tpu)
- [TPU System Architecture - Google Cloud Documentation](https://docs.cloud.google.com/tpu/docs/system-architecture-tpu-vm)
- [Ironwood: The first Google TPU for the age of inference](https://blog.google/innovation-and-ai/infrastructure-and-cloud/google-cloud/ironwood-tpu-age-of-inference/)
- [Introducing Trillium (v6e) - Google Cloud Blog](https://cloud.google.com/blog/products/compute/introducing-trillium-6th-gen-tpus)
- [How to Think About TPUs - JAX Scaling Book](https://jax-ml.github.io/scaling-book/tpus/)
- [Tensor Processing Unit - Wikipedia](https://en.wikipedia.org/wiki/Tensor_Processing_Unit)
