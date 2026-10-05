---
title: "LLM実行環境"
date: 2026-10-05
tags:
  - AI
  - LLM
  - Tool
related:
  - "[[Ollama]]"
  - "[[LMStudio]]"
  - "[[LLM]]"
  - "[[GPU]]"
  - "[[RAM]]"
  - "[[Artificial Analysis]]"
  - "[[llama.cpp]]"
  - "[[MLX]]"
  - "[[llm-jp]]"
  - "[[LLMキャラクター]]"
  - "[[Metal]]"
---
## 概要

Mac(M4 Air、RAM 32GB)で使うなら、**モデルを試すのはLM Studio、MLX形式で速度を出したいときはoMLXかmlx-lm、アプリ連携のAPIはOllamaのまま**、という使い分けがよさそうです。

## 比較

|環境|特徴|エンジン|向いている用途|
|---|---|---|---|
|Ollama|CLI + REST API、OSS|llama.cpp (+MLX対応が進行中)|開発者向けのバックグラウンドサーバー|
|LM Studio|GUI、非OSS|llama.cpp + MLX|モデル探索、対話|
|llama.cpp|CLI/ライブラリ|本体|速度検証、細かいチューニング|
|mlx-lm|CLI/Python|MLX|Apple Siliconでの最高速|
|oMLX|メニューバーアプリ + サーバー|MLX|並列リクエスト、長いコンテキスト|

Ollama、LM Studio、llama.cpp、mlx-lmの位置づけは、概ねこの表のとおりです。

## 分かったこと

- **速度の結果は測定によって割れています。** M4 Pro上のあるテストでは、LM StudioのMLXバックエンドがOllamaのllama.cppより46%多くトークンを生成しました(Qwen3-Coder-30B)。 一方、別の検証ではM3 MaxでLM StudioがOllamaより17%遅い結果でした。 同じ検証では、RAM 32GB以上のMacならOllamaを推奨しています。
- 大きな差が出ている例はMoEモデル(Qwen3-Coder-30B、Qwen3.6-35B-A3B)です。後者ではmlx-lmが163 tok/s、Ollamaが47〜55 tok/sでした。 今回のllm-jp-4.1 33bはDenseモデルで、同じ差が出るかは確認できていません。
- **MLXの最大のメリット**: mlx-lmはApple Silicon上で最速とされますが、CLI中心で機能は最小限です。 MLXはUnified MemoryとMetalを活かす設計で、Python中心の構成と相性がよいです。
- **oMLX**: 連続バッチ処理と、RAM・SSDの二層KVキャッシュを備えています。 macOS 15以降、Python 3.11〜3.13、Apple Siliconが必要で、メニューバーアプリとHomebrewの両方で入れられます。 長い文脈やツールを多用するワークロード向けの設計です。
- **Ollamaの強み**: 最初のトークンまでの時間は短く(175ms対291ms)、短い対話ではOllamaが有利でした。

## 用途別の選び方

- **新しいモデルをとりあえず試す**: LM Studio。GGUFとMLXの両方に対応しており、モデルの検索・ダウンロード・試用に向いています。
- **コーディング支援のAPI(エージェント連携など)**: Ollamaを継続。OpenAI互換のAPIが安定しており、周辺ツールも豊富です。
- **速度を詰めたい、長い文脈を使いたい**: oMLXかmlx-lm。Hugging FaceにMLX版があるモデル(hjmr氏版など)が前提です。
- **厳密に測定・調整したい**: llama.cpp。llama-benchで、プロンプト処理とトークン生成を設定ごとに測れます。

一般的な推奨も、LM Studioで探索、Ollamaでアプリ連携、llama.cppで調整、という使い分けです。 切り替えコストを抑えるには、LM Studioでllm-jp-4.1のGGUFとMLXの両方を同じプロンプトで試し、自分の環境での速度を比べるのが確実です。

なお、ファンレスのAirは長時間の生成で熱による速度低下が起きやすいと思われます(推測で、出典なし)。33Bクラスを使うなら、実機での継続的な測定をおすすめします。

## 注意

比較記事はベンダー寄りのものや個人の測定が多く、M4 Air + 32GBでの33B Denseモデルを直接測った資料は見つかりませんでした。

## 関連項目

- [[Ollama]] - CLI + REST API のローカル LLM サーバー．アプリ・エージェント連携向け
- [[LMStudio]] - GUI 付きのローカル LLM ツール．モデル探索・対話向け
- [[llama.cpp]] - 多くのローカル LLM 環境の基盤となる推論エンジン
- [[MLX]] - Apple Silicon 向けの機械学習フレームワーク（mlx-lm・oMLX の基盤）
- [[LLM]] - LLM 全般
- [[GPU]] - 推論速度を左右する演算資源（Apple Silicon では Unified Memory と Metal）
- [[RAM]] - モデルサイズ・コンテキスト長の上限を決めるメモリ容量
- [[Artificial Analysis]] - モデル横断のベンチマーク比較（実機測定との使い分け）
- [[llm-jp]]
- [[LLMキャラクター]]
- [[Metal]]

## 参考

- [Ollama vs LM Studio 2026(Morph)](https://www.morphllm.com/comparisons/ollama-vs-lm-studio)
- [Ollama vs LM Studio vs llama.cpp(Inventive HQ)](https://inventivehq.com/blog/ollama-vs-lm-studio-vs-llama-cpp)
- [llama.cpp vs Ollama vs LM Studio(PopularAI)](https://www.popularai.org/p/llama-cpp-vs-ollama-vs-lm-studio-speed)
- [Running LLMs Locally on macOS(DEV)](https://dev.to/bspann/running-llms-locally-on-macos-the-complete-2026-comparison-48fc)
- [jundot/omlx(GitHub)](https://github.com/jundot/omlx)
- [oMLXの解説(Pinggy)](https://pinggy.io/blog/omlx_local_llm_server_pinggy/)