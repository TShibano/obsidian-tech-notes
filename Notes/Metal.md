---
title: "Metal"
date: 2026-10-05
tags:
  - AI
  - ML
  - Tool
related:
  - "[[GPU]]"
  - "[[MLX]]"
  - "[[llama.cpp]]"
  - "[[LLM実行環境]]"
  - "[[RAM]]"
  - "[[LMStudio]]"
  - "[[Ollama]]"
  - "[[CPU]]"
  - "[[Arm]]"
  - "[[AI専用チップ]]"
  - "[[Vulkan]]"
  - "[[PyTorch]]"
---

## 概要

Metal は Apple が提供する低レベルのグラフィックス / 汎用計算 API で，Apple 自身の説明では「Apple silicon の性能を最大限引き出すための，緊密に統合されたモダンな API とシェーディング言語」である．2014 年に発表され，グラフィックスと汎用計算を 1 つの API と 1 つのシェーディング言語で扱う．Apple プラットフォームでは OpenGL / OpenCL の後継にあたる．最新世代は Metal 4（2025 年発表）．

## 詳細

### 登場の経緯

- 2014 年 6 月の WWDC 2014 で，iOS 8 向けの低オーバーヘッドな GPU プログラミング API として発表された．当初は A7 チップ向けに設計されていた．
- WWDC14 のセッションでは，グラフィックスと計算を 1 つの API と統一シェーディング言語で扱い，Render / Compute / Blit の 3 種のコマンドエンコーダを持つこと，シェーダをプリコンパイルでき，マルチスレッドでのコマンド生成が効率的なことが特徴として挙げられている．
- WWDC 2019 のセッションで，OpenGL / OpenGL ES / OpenCL は非推奨と説明された．「iOS 13 と macOS Catalina でも引き続きサポートされるが，移行の時期だ」とされた．Apple の OpenCL ページでも，OpenCL は macOS 10.14 で非推奨になったとして，Metal と Metal Performance Shaders への移行が推奨されている．
- OpenGL の暗黙的なコンテキスト管理に対し，Metal は Device / Command Queue / Command Buffer を明示的に扱い，シェーダを Xcode でビルド時にプリコンパイルできる点が違いとして挙げられている．

### グラフィックスと汎用計算

- 描画（レンダーパイプライン）と計算（コンピュートパイプライン）を同じ API で扱う．コンピュートシェーダは GPGPU に使われる．
- Metal Shading Language（MSL）は Metal のシェーダ記述言語で，C++ ベースの言語（既存知識）．Apple 公式は「Apple silicon を最大限活用するための強力なシェーディング言語」と位置づけている．
- Metal Performance Shaders（MPS）は最適化済みの計算・グラフィックスシェーダ群．MPSGraph は計算グラフを構築して実行するフレームワークで，Core ML モデルの統合にも使える．

### Metal 4

- WWDC 2025 で発表．対応するのは Apple M1 以降と A14 Bionic 以降の機器で，既存の Metal フレームワークの一部として提供される．
- 主な変更:
  - コマンドバッファをキューから切り離した新しいコマンド符号化モデル（`MTL4CommandQueue`，`MTL4CommandBuffer`）．
  - コンピュートエンコーダの統合（blit や acceleration structure の符号化も扱える）．
  - `MTL4Compiler` による明示的なコンパイル制御，柔軟なレンダーパイプライン状態．
  - 機械学習向けにテンソル（`MTLTensor`）を API と MSL に統合．機械学習コマンドエンコーダでネットワークを Metal アプリ内で直接実行できる．Metal Performance Primitives がテンソルを直接扱う．
  - MetalFX のフレーム補間とノイズ除去．
- WWDC 2026 のガイドでは，量子化テンソル形式とスケール係数のサポートが追加され，Metal Performance Primitives で重みを圧縮しつつ M5 Pro / M5 Max の Neural Accelerators を活用できるとされている．MetalFX の時間的アップスケーラも刷新され，Neural Engine と Neural Accelerators を使う．Game Porting Toolkit 4 も紹介されている．

### ユニファイドメモリとの関係

- Apple silicon では CPU と GPU が同じ物理メモリを共有するため，GPU バッファを CPU からそのまま参照でき，discrete GPU のような転送コピーが不要になる（既存知識）．[[MLX]] も「配列は共有メモリ上に存在する」とユニファイドメモリモデルを特徴に挙げる．
- 大きなモデルを載せられるかは GPU の VRAM ではなくシステムの [[RAM]] 容量に左右される（既存知識）．これがローカル LLM で Mac が選ばれる理由の 1 つ．

### 機械学習での利用

- PyTorch: MPS バックエンド．`torch.device("mps")` を指定すると，MPSGraph とチューニング済みカーネルに計算グラフとプリミティブをマップする．カスタム Metal カーネルも使える．最新安定版 PyTorch 2.11.0 の要件は Apple silicon，macOS 14.0 以降，Python 3.10 以降．未対応演算は `PYTORCH_ENABLE_MPS_FALLBACK=1` で CPU にフォールバックできる（フォールバックは二次情報）．
- [[MLX]]: Apple の機械学習フレームワーク．Apple 機器での GPU 実行に Metal を使う（MLX Swift の README に記載）．
- [[llama.cpp]]: README で「Apple silicon is a first-class citizen - optimized via ARM NEON, Accelerate and Metal frameworks」とされ，バックエンド表に Metal がある．[[LMStudio]] は Mac で llama.cpp と MLX をランタイムに使い，[[Ollama]] も llama.cpp と ggml を共有しているため，これらのツールでも Mac 上の GPU 推論は Metal が担う．

### Vulkan / DirectX との比較

- いずれも明示的な制御と低オーバーヘッドを狙った現代的な低レベル API．Metal 4 は DirectX など他 API の経験者に馴染みやすいことを意識して設計されたと Apple が説明している．
- 対応 OS: Metal は Apple プラットフォーム専用，DirectX は Windows / Xbox 中心，Vulkan はクロスプラットフォームだが Apple 純正では非対応（既存知識）．

### MoltenVK

- Khronos の MoltenVK は，Metal の上に Vulkan のサブセット（Vulkan Portability）を載せる実装．READMEでは Vulkan 1.4 の graphics / compute を macOS，iOS，tvOS，visionOS 上で提供するとされる．ライセンスは Apache 2.0．
- SPIR-V シェーダを実行時に MSL へ自動変換する．非公開 API を使わないため，App Store を含む通常の配布経路で使える．

## ポイント

- Metal = OpenGL + OpenCL 相当を統合した Apple 専用の低レベル API．2014 年登場．
- OpenGL / OpenCL は Apple 側で非推奨で，移行先は Metal．
- Metal 4（2025）はテンソルと機械学習エンコーダを API に統合し，Apple silicon 専用．
- ローカル LLM / ML では PyTorch MPS，MLX，llama.cpp が Metal を土台にしている．
- ユニファイドメモリにより，GPU が使えるメモリ量はシステム RAM に依存する．
- Vulkan アプリは MoltenVK で Metal 上に載せられる．

## 関連項目

- [[GPU]]
- [[MLX]]
- [[llama.cpp]]
- [[LLM実行環境]]
- [[RAM]]
- [[LMStudio]]
- [[Ollama]]
- [[CPU]]
- [[Arm]]
- [[AI専用チップ]]
- [[Vulkan]]
- [[PyTorch]]

## 参考

- [Discover Metal 4 - WWDC25](https://developer.apple.com/videos/play/wwdc2025/205/)
- [WWDC26 Metal guide - Apple Developer](https://developer.apple.com/wwdc26/guides/metal/)
- [Working with Metal: Overview - WWDC14](https://developer.apple.com/videos/play/wwdc2014/603/)
- [Bringing OpenGL Apps to Metal - WWDC19](https://developer.apple.com/videos/play/wwdc2019/611/)
- [OpenCL for macOS - Apple Developer](https://developer.apple.com/opencl/)
- [Metal - Apple Developer](https://developer.apple.com/metal/)
- [Accelerated PyTorch training on Mac - Apple Developer](https://developer.apple.com/metal/pytorch)
- [MLX (GitHub)](https://github.com/ml-explore/mlx)
- [MLX Swift (GitHub)](https://github.com/ml-explore/mlx-swift)
- [LM Studio Docs](https://lmstudio.ai/docs/app)
- [Ollama's new engine for multimodal models](https://ollama.com/blog/multimodal-models)
- [llama.cpp (GitHub)](https://github.com/ggml-org/llama.cpp)
- [MoltenVK (GitHub)](https://github.com/KhronosGroup/MoltenVK)
