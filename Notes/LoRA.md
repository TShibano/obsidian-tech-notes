---
title: "LoRA"
date: 2026-10-01
tags:
  - AI
  - ML
  - LLM
related:
  - "[[Nimble]]"
  - "[[Jev]]"
  - "[[Decision Model]]"
  - "[[GPU]]"
  - "[[ファインチューニング]]"
  - "[[QLoRA]]"
  - "[[Stable Diffusion]]"
---

## 概要

LoRA（Low-Rank Adaptation）は，事前学習済みモデルの重みを凍結し，各層に学習可能な低ランク分解行列を注入して追加学習する PEFT（パラメータ効率的ファインチューニング）手法である．Hu et al. が2021年に提案し，LLM だけでなく画像生成モデルの追加学習にも広く使われている．

## 詳細

### 生成 AI モデルの学習における位置づけ

1. **事前学習**: 大規模な汎用データでモデルの全パラメータを学習する．計算コストが極めて大きく，通常はモデル提供元だけが行う
2. **フルファインチューニング**: 事前学習済みモデルの全パラメータを特定タスク向けに再学習する．性能は高いが，オプティマイザ状態を含め大量のメモリが必要で，タスクごとに全パラメータのコピーを保存・配備する必要がある
3. **PEFT**: 全パラメータではなく少数の追加パラメータだけを学習する手法の総称．LoRA はその代表格で，アダプタ（差分）だけを保存・共有できる

LoRA の論文は，GPT-3 175B のような巨大モデルでは，タスクごとにフルファインチューニングしたインスタンスを個別に配備するのは非常に高価であることを動機に挙げている．

### LoRA の仕組み

事前学習済みの重み行列 W0 を凍結し，更新量を低ランク行列の積 BA で表す．

- 順伝播: `h = W0 x + B A x`（論文の式）
- W0 は d×k，B は d×r，A は r×k で，ランク r は min(d, k) よりずっと小さい
- 初期化: A はランダムなガウス分布，B はゼロ．学習開始時は ΔW = BA = 0 なので元のモデルと同じ出力になる
- スケーリング: ΔWx に `alpha / r` を掛ける．論文では最初に試した r を alpha に設定し，alpha は調整しない．PEFT の実装では `use_rslora=True` で `alpha / sqrt(r)` に変更できる
- 対象層: 原論文の実験の多くは，簡単のため自己注意の Wq と Wv だけに適用している．現在の実装では `target_modules` で指定し，`"all-linear"` で全線形層にも適用できる
- 推論時のマージ: 学習後に `W = W0 + (alpha/r) BA` として重みへ加算できるため，アダプタと違って推論の追加レイテンシがない．PEFT では `merge_and_unload()` でマージ済みモデルを得られる（インプレースではなく戻り値を受け取る）．`merge_adapter()` / `unmerge_adapter()` なら元に戻せる
- 学習後のアダプタは小さい．論文では GPT-3 175B に r = 4 で Wq と Wv だけを適用した場合，チェックポイントが 350GB から 35MB（約 10,000 分の 1）になると報告している．100 タスク分のモデルを保存しても 350GB + 35MB × 100 ≈ 354GB で済む（フルファインチューニングなら約 35TB）

### 論文の主な数値

原論文のアブストラクトによると，GPT-3 175B を Adam でファインチューニングする場合と比べて，学習可能パラメータ数を 10,000 分の 1，GPU メモリ要件を 3 分の 1 に削減できる．本文では学習時の VRAM 消費が 1.2TB から 350GB に減り，フルファインチューニング比で学習が 25% 高速になったと報告している．RoBERTa，DeBERTa，GPT-2，GPT-3 でフルファインチューニングと同等以上のモデル品質を示し，学習スループットも高い．

### 派生手法

| 手法 | 要点 |
|------|------|
| QLoRA | 凍結した 4bit 量子化モデルを通して勾配を伝播し LoRA を学習．NF4，二重量子化，ページドオプティマイザを導入．65B モデルを 48GB の単一 GPU でファインチューニング可能で，16bit のファインチューニングの性能を維持と報告 |
| DoRA | 重みを大きさ（magnitude）と方向（direction）に分解し，方向の更新に LoRA を使う．LLaMA，LLaVA，VL-BART で LoRA を一貫して上回ると報告．推論時の追加コストなし．PEFT では `use_dora=True` |
| LoRA+ | A と B に同じ学習率を使うのは幅の大きいモデルで最適でないとして，A と B に別々の学習率（比を適切に選ぶ）を設定する．1〜2%の性能向上，最大約2倍の高速化と報告 |
| rsLoRA | `alpha / sqrt(r)` でスケーリングし，高ランクでの安定性を改善（PEFT ドキュメントの記述） |
| PiSSA / VeRA など | 初期化の工夫（PiSSA は事前学習重みの特異ベクトルで初期化）やパラメータ共有による効率化（個別論文は未確認） |

### 実装

- **Hugging Face PEFT**: `LoraConfig(r=..., lora_alpha=..., target_modules=..., lora_dropout=...)` と `get_peft_model(model, config)` が基本．デフォルトは `r=8`，`lora_alpha=8`，`lora_dropout=0.0`．TRL の SFTTrainer などと組み合わせて使うのが一般的（TRL は既存知識）
- **diffusers（画像生成）**: `train_text_to_image_lora.py` などのサンプルスクリプトで，UNet の注意層（`to_k`，`to_q`，`to_v`，`to_out.0`）に LoRA を追加する．DreamBooth，SDXL，Kandinsky 2.2，Wuerstchen にも対応．`--rank` で rank を指定し，デフォルト学習率は 1e-4 だが LoRA ではより高い学習率も使える．出力は `pytorch_lora_weights.safetensors`（数百 MB 程度）で，`pipeline.load_lora_weights()` で読み込む．Kohya などコミュニティ製トレーナーの形式や複数 LoRA の併用も PEFT 経由で可能
- **Unsloth**: LoRA/QLoRA のファインチューニングを高速・省メモリ化するライブラリ．ガイドでは gradient checkpointing に `"unsloth"` を指定すると VRAM を約30%削減できるとしている

### ベストプラクティス

Unsloth のハイパーパラメータガイドと Thinking Machines の "LoRA Without Regret" を総合すると，以下が目安になる．

- rank: 16 か 32 から始める（候補は 8〜128）
- alpha: rank と同じ値が堅実な基準．2倍にすると学習が積極的になる
- 対象層: 注意層だけでなく MLP を含む全線形層に適用する．Thinking Machines は「注意層のみの rank 256 は MLP のみの rank 128 より劣る」と報告
- 学習率: Unsloth は通常 2e-4（DPO・GRPO などの強化学習では 5e-6）から開始．Thinking Machines は，最適学習率はフルファインチューニングの約10倍が目安と報告
- epoch: 1〜3．3 epoch を超えると効果は逓減
- 実効バッチサイズ: 16 程度（batch 2 × 勾配累積 8）
- dropout は 0，weight decay は 0.01〜0.1，warmup は全ステップの 5〜10%
- LoRA の容量がデータ量に対して不足しない限り，フルファインチューニングと同等になる．強化学習では rank 1 でも同等性能を示したと報告されている
- 一方，Biderman et al. "LoRA Learns Less and Forgets Less" は，プログラミングと数学の領域で，指示チューニング（約10万ペア）と継続事前学習（200億トークン）の両方を比較した．標準的な低ランク設定の LoRA はフルファインチューニングに大きく劣るが，対象領域外の性能は維持しやすい（忘却が少ない）．フルファインチューニングが学ぶ摂動のランクは典型的な LoRA 設定の 10〜100 倍だと報告しており，これが差の一因と考えられる

### 実践例

[[Nimble]] は Qwen3.5-9B を LoRA でファインチューニングした判定モデルで，2,676例・約2日の学習で社内評価では [[Jev]] に3ポイント差まで迫っている（Nimble ノートの記述）．重みは LoRA アダプタ形式で公開され，マージして使える．

### 最新動向

2026年時点でも PEFT の中心は LoRA 系である．Thinking Machines の "LoRA Without Regret" のように，フルファインチューニングと同等の性能を出すための条件を整理する研究が進んでいる．マルチ LoRA の混合や連合学習への応用も研究されている（個別論文は未確認）．

## ポイント

- LoRA は W0 を凍結し，低ランク BA だけを学習する．B の初期値がゼロなので学習開始時の出力は元モデルと同一
- 推論時はマージでき，追加レイテンシがない．アダプタは小さく，タスクごとの切り替えや配布が容易
- 論文の数値: GPT-3 175B で学習可能パラメータ 10,000 分の 1，GPU メモリ 3 分の 1
- 実践では全線形層に適用し，rank 16〜32，学習率はフルファインチューニングより高めにする
- LoRA はデータ量が多い領域学習では不利になりうるが，忘却は少ない
- メモリがさらに厳しいなら QLoRA，精度重視なら DoRA を検討する

## 関連項目

- [[Nimble]]
- [[Decision Model]]
- [[GPU]]
- [[ファインチューニング]]
- [[QLoRA]]
- [[Stable Diffusion]]
- [[Jev]]

## 参考

- [LoRA: Low-Rank Adaptation of Large Language Models (arXiv:2106.09685)](https://arxiv.org/abs/2106.09685)
- [QLoRA: Efficient Finetuning of Quantized LLMs (arXiv:2305.14314)](https://arxiv.org/abs/2305.14314)
- [DoRA: Weight-Decomposed Low-Rank Adaptation (arXiv:2402.09353)](https://arxiv.org/abs/2402.09353)
- [LoRA+: Efficient Low Rank Adaptation of Large Models (arXiv:2402.12354)](https://arxiv.org/abs/2402.12354)
- [LoRA Learns Less and Forgets Less (arXiv:2405.09673)](https://arxiv.org/abs/2405.09673)
- [LoRA Without Regret - Thinking Machines Lab](https://thinkingmachines.ai/blog/lora/)
- [PEFT LoRA リファレンス - Hugging Face](https://huggingface.co/docs/peft/package_reference/lora)
- [LoRA 学習ガイド - diffusers](https://huggingface.co/docs/diffusers/training/lora)
- [LoRA Hyperparameters Guide - Unsloth](https://unsloth.ai/docs/get-started/fine-tuning-llms-guide/lora-hyperparameters-guide)
