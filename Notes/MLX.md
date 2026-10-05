---
title: "MLX"
date: 2026-10-05
tags:
  - AI
  - ML
  - LLM
related:
  - "[[Metal]]"
  - "[[LLM実行環境]]"
  - "[[LMStudio]]"
  - "[[Ollama]]"
  - "[[GPU]]"
  - "[[RAM]]"
  - "[[LoRA]]"
  - "[[llama.cpp]]"
---

## 概要
MLX は Apple の機械学習研究チームが開発する，Apple silicon 向けに最適化された配列（array）フレームワーク．ユニファイドメモリと遅延評価を前提とし，NumPy / PyTorch に近い API で，Mac 上での LLM 推論やファインチューニングに使われる．

## 詳細

### 設計の特徴
- **ユニファイドメモリ**: MLX の配列は共有メモリ上にあり，CPU と GPU のどちらでも，データ転送なしに演算できる（[[RAM]] と [[GPU]] が同一メモリを共有する Apple silicon の構造が前提）．
- **遅延評価**: 計算は必要になるまで実行されず，配列は必要時に実体化される．
- **動的グラフ**: 計算グラフは動的に構築され，引数の shape が変わっても低速な再コンパイルが起きない．
- **関数変換**: 自動微分，自動ベクトル化，計算グラフ最適化を合成可能な関数変換として提供する．
- **API**: Python，C++，C，Swift．`mlx.nn` と `mlx.optimizers` は PyTorch に近い書き味．設計は NumPy，PyTorch，JAX，ArrayFire から着想を得ている．
- GPU 実行は Apple 環境では [[Metal]] を使う．なお MLX Swift の README には Linux 上の CUDA での GPU 実行も記載がある（CUDA バックエンドは MLX 本体のリリースノートにも登場する）．

### Swift API
MLX Swift は MLX を Swift に拡張するもので，MLX（コア），MLXNN，MLXOptimizers の 3 ライブラリを持つ．iOS / macOS アプリへの組み込みに使え，LLM / VLM の実装は別リポジトリ mlx-swift-lm にある．サンプルは mlx-swift-examples（MLXChatExample，LLMEval など）．

### mlx-lm によるローカル LLM
mlx-lm は Apple silicon 上でのテキスト生成とファインチューニングのためのパッケージ．

- Hugging Face Hub 連携で多数のモデルを利用でき，量子化に対応する．
- インストール: `pip install mlx-lm`（または conda-forge）．
- 生成: `mlx_lm.generate --prompt "..."`，対話: `mlx_lm.chat`．
- 量子化: `mlx_lm.convert --model <repo> -q`．
- `mx.distributed` による分散推論に対応する．

### LoRA ファインチューニング
- 学習: `mlx_lm.lora --model <path> --train --data <path> --iters 600`．
- `--fine-tune-type` で LoRA（既定），DoRA，full を選べる．
- 量子化済みモデルを指定すると QLoRA として動く（[[LoRA]] 参照）．
- データは JSONL．chat，completions，tools，text の形式に対応する．
- `mlx_lm.fuse` でアダプタをベースモデルに統合し，Hub へのアップロードや GGUF へのエクスポート（Mistral / Mixtral / Llama の fp16）ができる．
- メモリ節約: QLoRA，`--batch-size` を下げる，`--num-layers` を減らす，`--grad-checkpoint`，長い例を分割する．32GB の例として `--batch-size 1 --num-layers 4`（Mistral-7B）が挙げられている．

### 最新動向: M5 の Neural Accelerators
Apple の ML Research ブログ（2025-11-19）によると，MLX は M5 の GPU 内 Neural Accelerators（行列乗算専用演算）を活用でき，macOS 26.2 以降が必要．M4 比で次の数値が報告されている（Apple 自身の計測）．

- 最初のトークンまでの時間（TTFT）: 3.33 倍から 4.06 倍高速．
- 以降のトークン生成: 19% から 27% 高速．生成は計算能力ではなくメモリ帯域に律速されるとされ，帯域は M4 の 120GB/s に対し M5 は 153GB/s（28% 増）．
- FLUX-dev-4bit の画像生成: M4 比で 3.8 倍超．

つまりプロンプト処理（計算律速）は大きく伸び，トークン生成（帯域律速）の伸びは帯域の増分程度にとどまる．

### 他ランタイムとの位置づけ
[[llama.cpp]]，[[Ollama]]，[[LMStudio]] と並ぶ [[LLM実行環境]] の選択肢．Metal とユニファイドメモリを前提に最適化されているのが強み．[[LMStudio]] は Apple silicon Mac で llama.cpp（GGUF）に加えて MLX をランタイムとして使え，MLX 形式のモデルを実行できる．

## ポイント
- ユニファイドメモリ前提のため CPU / GPU 間コピーが不要．
- 遅延評価と動的グラフで，研究向けの柔軟さを保つ．
- Python / Swift / C++ / C の API．Swift でアプリ組み込みも可能．
- mlx-lm で推論・量子化・LoRA / DoRA / full の学習まで Mac 上で完結する．
- M5 の Neural Accelerators 対応は macOS 26.2 以降が必要．改善はプロンプト処理で顕著．

## 関連項目
- [[Metal]]
- [[LLM実行環境]]
- [[LMStudio]]
- [[Ollama]]
- [[GPU]]
- [[RAM]]
- [[LoRA]]
- [[llama.cpp]]

## 参考
- [ml-explore/mlx (GitHub)](https://github.com/ml-explore/mlx)
- [MLX ドキュメント](https://ml-explore.github.io/mlx/build/html/index.html)
- [ml-explore/mlx-lm (GitHub)](https://github.com/ml-explore/mlx-lm)
- [mlx-lm LORA.md](https://github.com/ml-explore/mlx-lm/blob/main/mlx_lm/LORA.md)
- [ml-explore/mlx-swift (GitHub)](https://github.com/ml-explore/mlx-swift)
- [LM Studio Docs](https://lmstudio.ai/docs/app)
- [Exploring LLMs with MLX and the Neural Accelerators in the M5 GPU (Apple ML Research)](https://machinelearning.apple.com/research/exploring-llms-mlx-m5)
