---
title: "GPU"
date: 2026-05-10
tags:
  - Cloud
  - AI
related:
  - "[[CPU]]"
  - "[[TPU]]"
  - "[[AI専用チップ]]"
  - "[[フォン・ノイマンアーキテクチャ]]"
  - "[[LoRA]]"
  - "[[MLX]]"
  - "[[llama.cpp]]"
---

## 概要

GPU（Graphics Processing Unit）は，もともとグラフィックスレンダリング向けに開発された大規模並列演算プロセッサ．数千〜数万のコアを持ち，SIMD/SIMT モデルで同一命令を大量データに適用するのが得意．現在はAI学習・推論や科学計算にも広く使われる（GPGPU）．

## 詳細

### CPU との比較

| 比較項目 | [[CPU]] | GPU |
|---------|--------|-----|
| コア数 | 数〜100 程度 | 数千〜数万 |
| 処理の得意分野 | 複雑な逐次制御フロー | 単純演算の大規模並列 |
| クロック周波数 | 高い（数GHz） | 低い（1〜2GHz 程度） |
| キャッシュ容量 | 大きい | 小さい（共有メモリで補完） |
| メモリ帯域幅 | 低い | 非常に高い（HBM等） |

### アーキテクチャの仕組み

- **CUDA コア（NVIDIA）/ Stream Processor（AMD）**: 浮動小数点演算ユニット
- **SM（Streaming Multiprocessor）**: 複数のコアをまとめた実行ブロック
- **SIMT（Single Instruction, Multiple Threads）**: 1命令を多数のスレッドで同時実行
- **VRAM（Video RAM）**: 高帯域幅メモリ（HBM2e/HBM3/GDDR6X 等）

### NVIDIA アーキテクチャの世代

| 世代 | リリース | 主な特徴 |
|------|---------|---------|
| Ampere (A100) | 2020 | Transformer Engine，sparsity サポート |
| Hopper (H100) | 2022 | NVLink 4.0，FP8 サポート |
| Blackwell (B100/B200) | 2024 | GB200 NVL72，FP4 推論 |
| Rubin | 2026 予定 | 次世代アーキテクチャ |

### 主な用途

1. **ゲーム・グラフィックス**: リアルタイムレンダリング，レイトレーシング
2. **AI 学習（Training）**: 大規模 LLM の分散学習
3. **AI 推論（Inference）**: リアルタイム推論サービス
4. **科学計算**: 分子動力学，気象シミュレーション
5. **データ処理**: RAPIDS によるGPU加速 SQL

## ポイント

- GPU 市場は2025年時点で826.8億ドル規模，AIブームで急拡大
- [[TPU]] や [[AI専用チップ]] は特定用途でGPUを置き換えつつある
- NVIDIA が市場を独占しているが，AMD MI300X，Intel Gaudi 2/3 が追随
- 消費電力・発熱が課題（Blackwell は1ラック数百kW 超）

## 関連項目

- [[CPU]]
- [[TPU]]
- [[AI専用チップ]]
- [[フォン・ノイマンアーキテクチャ]]
- [[RAM]]
- [[LoRA]]
- [[MLX]]
- [[llama.cpp]]

## 参考

- [NVIDIA Blackwell アーキテクチャ](https://www.nvidia.com/en-us/data-center/technologies/blackwell-architecture/)
- [GPU とは？CPU との違い - FPT Japan](https://fptsoftware.jp/resource-center/blog/column/2025-08-gpu)
- [NVIDIA GPU アーキテクチャ解説 - ORIX](https://go.orixrentec.jp/rentecinsight/it/article-727)
