---
title: "llama.cpp"
date: 2026-10-05
tags:
  - AI
  - LLM
  - Tool
  - CLI
related:
  - "[[Ollama]]"
  - "[[LMStudio]]"
  - "[[LLM実行環境]]"
  - "[[Metal]]"
  - "[[GPU]]"
  - "[[RAM]]"
  - "[[GGUF]]"
  - "[[MLX]]"
  - "[[ggml]]"
  - "[[量子化]]"
---

## 概要

llama.cpp は，依存なしの純粋な C/C++ で LLM / VLM の推論を行うオープンソース（MIT）のランタイムである．Apple Silicon，NVIDIA GPU，AMD GPU，Vulkan，CPU など幅広いハードウェアで動作し，[[Ollama]] や [[LMStudio]] などローカル LLM ツールの推論基盤にも使われてきた．2026-02 に GGML / llama.cpp は Hugging Face に合流した．

## 詳細

### アーキテクチャ

- **ggml**: llama.cpp が土台とするテンソルライブラリ（[[ggml]]）．各ハードウェア向けバックエンドはこの層で実装される
- **GGUF**: モデル配布用のバイナリ形式（[[GGUF]]）
  - 先頭のマジックナンバーは `GGUF`，仕様上のバージョンは 3
  - ハイパーパラメータ等を型付きの key-value メタデータとして持ち，拡張しても互換性を壊しにくい
  - テンソル情報（名前，次元，型，オフセット）を持ち，アラインメントされているため `mmap` で高速に読み込める
  - モデルの読み込みに必要な情報がすべて 1 ファイルに入る（single-file deployment）
- **量子化**: 1.5bit 〜 8bit の整数量子化に対応し，メモリ使用量と推論時間を削減する．CPU と GPU のハイブリッド推論により VRAM を超えるモデルも動かせる（[[量子化]]）

### 量子化形式

`llama-quantize` で高精度の GGUF を量子化する．公式 README の Llama-3.1-8B での値の例は次のとおり．

| 種類 | bits/weight | サイズ (GiB) |
|------|-------------|--------------|
| IQ1_S | 2.00 | 1.87 |
| IQ2_XXS | 2.38 | 2.23 |
| Q2_K_S | 2.97 | 2.78 |
| Q3_K_M | 3.99 | 3.74 |
| Q4_K_M | 4.89 | 4.58 |
| Q5_K_M | 5.70 | 5.33 |
| Q8_0 | 8.50 | 7.95 |
| F16 | 16.00 | 14.96 |

- `Q4_0` などは従来型のブロック量子化，`Q*_K` は k-quant（ブロック単位で混合精度），`IQ*` は低ビット向けの方式．末尾の `S` / `M` は Small / Medium で，M の方が一部テンソルを高精度にして品質が高い（既存知識）
- Q4_K_M は品質とサイズのバランスが良く，ローカル用途で最も使われる選択肢の一つ（二次情報）
- `--imatrix` で重要度行列（importance matrix）を与えると，量子化による精度劣化を抑えられる

```bash
./llama-quantize --imatrix imatrix.gguf input-model-f32.gguf q4_k_m 8
```

### 対応バックエンド

公式のビルド手順に載っているバックエンドは次のとおり．

| バックエンド | 対象 |
|--------------|------|
| Metal | Apple Silicon．macOS ではデフォルトで有効（[[Metal]]） |
| CUDA | NVIDIA GPU |
| HIP | AMD GPU（ROCm） |
| Vulkan | クロスプラットフォーム GPU |
| SYCL | Intel GPU |
| MUSA | Moore Threads GPU |
| OpenCL | Android の Adreno GPU |
| WebGPU | ブラウザ（Dawn） |
| OpenVINO | Intel |
| CANN | Ascend NPU |
| ZenDNN | AMD EPYC CPU |
| KleidiAI | Arm CPU マイクロカーネル |

- 複数バックエンドを同時にビルドでき（例: `-DGGML_CUDA=ON -DGGML_VULKAN=ON`），実行時に `--device` で選択，`--list-devices` で一覧表示する
- CPU 側は ARM NEON / Accelerate（Apple Silicon），AVX / AVX512（x86）などで最適化されている
- 基本ビルドは `cmake -B build` と `cmake --build build --config Release`

### ツール群

- **llama-cli**: コマンドラインでの対話・生成
- **llama-server**: 軽量な C/C++ 製 REST サーバ．OpenAI 互換の chat completions / responses / embeddings ルートと，独自の `/completion`，`/embedding`，`/tokenize` などを持つ．組み込みの Web UI，並列デコード（マルチユーザ），continuous batching，speculative decoding，マルチモーダル入力，スキーマ指定の JSON 出力，slots / metrics による監視に対応する
- **llama-quantize**: GGUF の量子化
- **GBNF 文法**: 文法による構造化出力
- そのほか llama-completion など

```bash
llama-server -m model.gguf --port 8080
```

### llama-server のルーターモード

2025-12-11 の公式ブログで，モデルを再起動なしに切り替えられる router mode が紹介された．「Ollama 的な機能」をほしいという要望に応えたものである．

- 起動時にモデルを指定しない `llama-server` で，キャッシュまたは `--models-dir` 内の GGUF を自動検出する
- リクエストの `model` フィールドで振り分け，初回リクエスト時にロードする
- 同時ロード数の上限は既定 4 で，超えると LRU で追い出す（`--models-max`）
- モデルごとに別プロセスで動作し，`/models`，`/models/load`，`/models/unload` で管理する

### Ollama / LM Studio との関係

- [[Ollama]] はもともとモデル対応を llama.cpp に依存していたが，2025-05 にマルチモーダル対応の独自エンジンを発表した．新エンジンも ggml は Go から直接利用しており，llama.cpp とは ggml を共有する関係になっている
- [[LMStudio]] は Mac / Windows / Linux で llama.cpp（GGUF）を使い，Apple Silicon Mac では [[MLX]] でも実行できる
- llama.cpp 自体は GGUF を直接扱う低レベルかつ最も自由度の高い層で，Ollama や LM Studio はモデル管理・UI・配布を上乗せしたラッパーと位置づけられる．詳しくは [[LLM実行環境]] を参照

### 最近の動向

- 2026-02-20: GGML / llama.cpp が Hugging Face に合流．「llama.cpp will continue to be open-source and free to use」とされ，開発は従来どおり進み，法務・財務・採用・マーケティングを Hugging Face が担う
- 2025-12: llama-server に router mode
- GitHub のスター数は約 13 万（2026-10-05 時点）
- リリースは `bNNNN` 形式の連番タグで，GitHub Actions が各 OS 向けバイナリ（CUDA / Vulkan / ROCm など）を自動配布する．2026-10-05 時点の最新タグは b11418

## ポイント

- llama.cpp は ggml 上に構築された推論エンジンで，モデル形式は GGUF
- 量子化は Q4_K_M あたりが標準的な出発点で，低ビットでは imatrix が有効
- Mac では Metal が既定で有効になり，統合メモリ（[[RAM]]）の量がモデルサイズの上限を決める
- llama-server は OpenAI 互換 API を提供し，router mode で複数モデルを切り替えられる
- 複数バックエンドの同時ビルドと，CPU+GPU ハイブリッド推論が可能
- LM Studio は llama.cpp をランタイムとして使い，Ollama も ggml を共有するため，ローカル LLM エコシステムの基盤になっている

## 関連項目

- [[Ollama]]
- [[LMStudio]]
- [[LLM実行環境]]
- [[Metal]]
- [[GPU]]
- [[RAM]]
- [[GGUF]]
- [[ggml]]
- [[MLX]]
- [[量子化]]

## 参考

- [ggml-org/llama.cpp (GitHub)](https://github.com/ggml-org/llama.cpp)
- [GGUF 仕様 (ggml docs/gguf.md)](https://github.com/ggml-org/ggml/blob/master/docs/gguf.md)
- [llama-quantize README](https://github.com/ggml-org/llama.cpp/blob/master/tools/quantize/README.md)
- [llama.cpp ビルドドキュメント](https://github.com/ggml-org/llama.cpp/blob/master/docs/build.md)
- [llama-server README](https://github.com/ggml-org/llama.cpp/blob/master/tools/server/README.md)
- [New in llama.cpp: Model Management (Hugging Face Blog)](https://huggingface.co/blog/ggml-org/model-management-in-llamacpp)
- [GGML and llama.cpp join Hugging Face](https://huggingface.co/blog/ngxson/ggml-and-llama-cpp-join-hugging-face)
- [llama.cpp Releases](https://github.com/ggml-org/llama.cpp/releases)
- [Ollama's new engine for multimodal models (Ollama Blog)](https://ollama.com/blog/multimodal-models)
- [LM Studio Docs](https://lmstudio.ai/docs/app)
