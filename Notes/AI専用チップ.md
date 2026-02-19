---
title: "AI専用チップ"
date: 2026-02-19
tags:
  - AI
  - ML
related:
  - "[[TPU]]"
  - "[[MLOps]]"
  - "[[Gemini]]"
---

## 概要

AI専用チップとは、機械学習・深層学習のワークロードに特化して設計されたプロセッサの総称。汎用 CPU と異なり、行列演算・テンソル演算を高効率で処理する回路構成を持ち、AI 学習・推論の消費電力あたり性能（電力効率）を大幅に向上させる。GPU・TPU・NPU・ASIC・FPGA などの種別がある。

## 詳細

### チップ種別の概要

| 種別 | 主な用途 | 特徴 |
|------|---------|------|
| **GPU** | 学習・推論（汎用） | 大規模な並列演算コア。AI 以外にも利用可能。エコシステムが最大 |
| **TPU** | 学習・推論（ML特化） | Google 設計の ASIC。シストリックアレイで行列乗算を専用処理。[[TPU]] 参照 |
| **NPU** | エッジ推論 | ニューラルネット推論に特化。低消費電力。スマートフォン・PC に搭載 |
| **ASIC** | 特定ワークロード | 用途固定の専用回路。最高効率を実現。汎用性はなし |
| **FPGA** | 試作・柔軟な推論 | 回路を再プログラム可能。電力効率は高いが開発コスト大 |

### GPU（Graphics Processing Unit）

GPU は数千の小型コアで大規模な並列演算を実行する。AI 学習においてデファクトスタンダードとなっており、ソフトウェアエコシステム（CUDA）が最も充実している。

#### NVIDIA 主要 GPU

| モデル | 世代 | HBM容量 | 主な特徴 |
|--------|------|---------|---------|
| **H100** | Hopper | 80 GB | ~1,000 TFLOPS。データセンター向け学習・推論の主力 |
| **H200** | Hopper | 141 GB | H100 比で HBM 帯域幅・容量を大幅強化 |
| **B100 / B200** | Blackwell | 192 GB | H100 比 2〜3 倍の性能を目指す次世代 GPU |
| **GB200** | Blackwell | NVLink 接続 | CPU+GPU の Grace Blackwell Superchip 構成 |

### TPU（Tensor Processing Unit）

Google が開発した AI 学習・推論専用 ASIC。シストリックアレイ構造で行列乗算を高速処理し、GPU 比で電力効率に優れる。詳細は [[TPU]] を参照。

- **v6e（Trillium）**: v5e 比 4.7 倍の性能向上・67% のエネルギー効率改善（2024年）
- **v7（Ironwood）**: 推論特化設計。9,216 チップ時に 42.5 Exaflops。[[Gemini]] の推論インフラ（2025年）

### NPU（Neural Processing Unit）

ニューラルネットワークの推論処理に特化したプロセッサ。エッジデバイス（スマートフォン・PC・IoT）での低消費電力 AI 推論を実現する。

- Apple の Neural Engine（iPhone・Mac 搭載）
- Qualcomm Hexagon DSP（Android 向け）
- Intel AI Boost（Meteor Lake CPU 統合）
- AMD XDNA アーキテクチャ（Ryzen AI シリーズ）
- 最新 3nm プロセス NPU: 前世代比 ~30% 電力削減・~15% 性能向上

### カスタム ASIC（クラウドプロバイダ製）

主要クラウドプロバイダは NVIDIA 依存を削減するため独自 AI チップを開発している。

| チップ | 開発元 | 概要 |
|--------|--------|------|
| **Trainium2** | AWS | 16 チップで 20.8 petaFLOPS（FP8）。Trainium1 比 4 倍性能 |
| **Trainium3** | AWS | 3nm プロセス。Trainium2 比 4.4 倍コンピュート・4 倍メモリ帯域幅 |
| **Inferentia** | AWS | 推論特化 ASIC。Trainium と連携した学習〜推論の一貫基盤 |
| **MTIA** | Meta | 推薦システム・広告 AI 向けカスタムチップ |
| **Maia 100** | Microsoft | Azure AI 向けカスタム AI アクセラレータ |

### 市場動向（2025〜2026年）

- **市場規模**: AI 半導体市場は 2025年時点で約 700 億ドル、2030年には 2,000 億ドル超に成長見込み
- **製造プロセス**: TSMC・Samsung が 2nm プロセスの量産化を進行中
- **クラウド競争**: AWS・Google が自社チップで NVIDIA との差を縮める方向
- **エッジ AI 拡大**: NPU 搭載デバイスの普及で、クラウドに依存しない推論が加速
- **推論シフト**: 大規模モデルの普及により、学習より推論コストが支配的になりつつある

## ポイント

- GPU（NVIDIA）が AI 学習のデファクトだが、TPU・カスタム ASIC による競争が激化
- NPU はエッジデバイスへの AI 普及を促進し、スマートフォン・PC での AI 処理を担う
- AWS・Google・Meta・Microsoft がそれぞれ NVIDIA 依存削減のためカスタム ASIC を開発
- 市場は 2030年に 2,000 億ドル超に成長予測。製造プロセスは 2nm 時代へ移行中
- 用途に応じた「適材適所」が重要: 汎用性 → GPU、高効率 ML → TPU、エッジ → NPU

## 関連項目

- [[TPU]] - Google の ML 専用 ASIC。詳細なアーキテクチャと世代別情報
- [[MLOps]] - AI チップを活用したモデルの学習・デプロイ運用
- [[Gemini]] - Google の LLM。TPU v7（Ironwood）を推論インフラとして活用

## 参考

- [CPU vs GPU vs NPU vs TPU: Complete Guide to AI Chips 2025](https://guptadeepak.com/understanding-cpus-gpus-npus-and-tpus-a-simple-guide-to-processing-units/)
- [AI半導体入門｜GPU・TPU・NPUの違いと選び方を徹底解説【2026年最新】 | ainow](https://ainow.jp/ai-semiconductor-guide/)
- [NVIDIA GPUs vs Google TPUs vs AWS Trainium | CNBC](https://www.cnbc.com/2025/11/21/nvidia-gpus-google-tpus-aws-trainium-comparing-the-top-ai-chips.html)
- [Amazon Trainium2 Architecture | SemiAnalysis](https://newsletter.semianalysis.com/p/amazons-ai-self-sufficiency-trainium2-architecture-networking)
- [CPU, GPU, TPU & NPU: What to Use for AI Workloads (2026 Guide) | Fluence](https://www.fluence.network/blog/cpu-gpu-tpu-npu-guide/)
