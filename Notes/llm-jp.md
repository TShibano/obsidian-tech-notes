---
title: "llm-jp"
date: 2026-10-05
tags:
  - AI
  - LLM
  - NLP
related:
  - "[[LLM実行環境]]"
  - "[[Ollama]]"
  - "[[LMStudio]]"
  - "[[GPT Model]]"
  - "[[LoRA]]"
  - "[[RAG]]"
  - "[[Mixture of Experts]]"
  - "[[Swallow]]"
  - "[[LLMキャラクター]]"
---

## 概要

LLM-jp は，国立情報学研究所（NII）の大規模言語モデル研究開発センターが主宰する研究者コミュニティの名称であり，同時に日本発のオープンな LLM 開発プロジェクトの名称でもある．コーパス，モデル，評価ツールを Apache License 2.0 などのオープンなライセンスで公開しており，2026-10-05 時点の最新モデルは 2026-09-28 公開の LLM-jp-4.1 シリーズ．

## 詳細

### プロジェクトの位置付け

- 公式サイトでは，自然言語処理や生成 AI 分野の研究者・開発者が集い，LLM の研究開発に関する情報を共有するコミュニティと説明されている．目的の1つは「オープンかつ日本語に強い大規模モデルの構築とそれに関連する研究開発の推進」．
- 2024年7月の論文（arXiv:2407.03963）では，産学にまたがる 1,500 人以上の参加者を持つ組織横断プロジェクトとして紹介されている（abs ページの要約による．参加者数は論文時点の値）．
- NII の 2026-04-03 のプレスリリースでは，コミュニティの参加者は産官学の 2,600 名以上（2026-03-31 時点）とされている．
- 関連リポジトリは GitHub の llm-jp 組織にあり，コーパス（llm-jp-corpus），評価（llm-jp-eval，llm-jp-judge），トークナイザ（llm-jp-tokenizer），SFT（llm-jp-sft）などが公開されている．

### モデルの系譜

| 世代 | 公開時期 | 概要 |
|---|---|---|
| LLM-jp-3 | （日付未確認） | 150M〜172B．llm-jp-corpus v3 で学習．13B は 2.1T トークン．172B は利用に承認が必要で再配布等に制限 |
| LLM-jp-3 MoE | 2025-03-26 | 8x1.8B（総 9.2B / 活性 2.9B），8x13B（総 73B / 活性 22B）．Drop-Upcycling を採用．Apache 2.0 |
| LLM-jp-3.1 | （日付未確認） | 8x13B，13B，1.8B の instruct |
| LLM-jp-4 | 2026-04-03 | 8B（Dense，約 86 億）と 32B-A3B（MoE）．約 12 兆トークンのコーパスで学習．日本語 MT-Bench で 8B が 7.54（NII 発表では GPT-4o は 7.29） |
| LLM-jp-4 33B | 2026-08-18 | 約 332 億パラメータの Dense モデル（base / thinking）．thinking は SFT + DPO．学習に使った DPO データセットも公開 |
| LLM-jp-4-VL 9B | 2026-09-01 | llm-jp-4-8b-thinking をマルチモーダル化した視覚言語モデル |
| LLM-jp-4.1 | 2026-09-28 | 8b / 32b-a3b / 33b の thinking．STEM データ拡充，ツール呼び出し対応，回答の冗長さの改善．SFT + DPO．次の LLM-jp-4.2 では強化学習の導入を予定 |

### LLM-jp-4 の仕様（Hugging Face モデルカードより）

- llm-jp-4-8b-instruct: Dense，32 層，hidden 4,096，32 ヘッド，総パラメータ 8,590,200,832，コンテキスト長 65,536．事前学習と中間学習で計 11.7T トークン．instruct は SFT のみ．
- llm-jp-4-32b-a3b-thinking: MoE，128 ルーティングエキスパート中 8 を活性化，総 32.1B / 活性 3.8B．post-training は SFT と DPO で，強化学習は使っていない．
- いずれも Apache License 2.0．

### コーパス

- llm-jp-3-13b のモデルカードによれば，学習コーパスは日本語（Common Crawl，Wikipedia，論文等）約 1T，英語（Dolma 等）約 950B，コード（The Stack）114.1B，中韓語約 1B トークン．トークナイザは llm-jp-tokenizer v3.0（Unigram byte-fallback）．
- LLM-jp-4 は，インターネット上の公開データや政府・国会の文書などからなる約 12 兆トークンのコーパスで学習したと NII が発表している（言語別の内訳は未確認）．

### 評価

- **llm-jp-eval**: 既存の日本語評価データを生成タスク形式に変換して自動評価するツール．Apache License 2.0（各データセットのライセンスは DATASET.md に別記）．推論は vLLM / Transformers / TensorRT-LLM，または OpenAI 互換サーバーに対応．評価データから指示チューニング用データ jaster も生成できる．
- **llm-jp-judge**: 生成結果の LLM-as-a-judge 自動評価ツール．
- 3 MoE の評価では llm-jp-eval v1.4.1，Japanese MT Bench，安全性の AnswerCarefully-Eval が使われた．LLM-jp-4 のモデルカードでは GPT-5.4 を評価者とした MT-Bench が報告されている（32b-a3b-thinking で日本語 7.82，英語 7.86．medium reasoning effort）．

### ライセンス

- LLM-jp-3 の主要モデル，3 MoE，LLM-jp-4，LLM-jp-4.1 は Apache License 2.0．
- 例外: LLM-jp-3 172B の一部は承認制で，再配布や一部用途に制限がある．

### 他の国産日本語 LLM との位置付け

- Swallow（東京科学大・産総研）と並ぶオープンウェイトの国産日本語モデルだが，Swallow は既存モデルへの継続事前学習が主なのに対し，LLM-jp は学習データから公開し，フルスクラッチ学習を行う点が特徴（この対比は既存知識による理解．公式での直接比較は未確認）．
- ELYZA は Llama ベース，Sarashina（ソフトバンク），PLaMo（Preferred Networks）は企業主導のモデル．LLM-jp は産学コミュニティによるオープン開発という位置付け（二次情報による整理）．
- 実行は [[Ollama]] や [[LMStudio]] 等の [[LLM実行環境]] で行える（GGUF 等の変換版の有無は未確認）．用途に応じて [[LoRA]] による追加学習や [[RAG]] との組み合わせが考えられる．

## ポイント

- 最新は LLM-jp-4.1（2026-09-28）．8B Dense / 32B-A3B MoE / 33B Dense の thinking モデル．
- コーパス，学習コード，評価ツールまで公開される透明性の高いプロジェクト．
- 主要モデルは Apache 2.0 で商用利用しやすい（172B 等の例外に注意）．
- コンテキスト長は LLM-jp-4 で 65,536 トークン．
- 評価は llm-jp-eval（タスク型）と llm-jp-judge / MT-Bench（LLM 判定）の併用．

## 関連項目

- [[LLM実行環境]]
- [[Ollama]]
- [[LMStudio]]
- [[GPT Model]]
- [[LoRA]]
- [[RAG]]
- [[Mixture of Experts]]
- [[Swallow]]
- [[LLMキャラクター]]

## 参考

- [LLM-jp 公式サイト](https://llm-jp.nii.ac.jp/)
- [LLM-jp Release](https://llm-jp.nii.ac.jp/en/release-en/)
- [NII LLMC トピック一覧](https://llmc.nii.ac.jp/topics/)
- [LLM-jp-4 8B / 32B-A3B の公開（NII プレスリリース）](https://www.nii.ac.jp/news/release/2026/0403.html)
- [LLM-jp-4 33B の公開](https://llm-jp.nii.ac.jp/news/20260818/)
- [LLM-jp-4.1 の公開](https://llmc.nii.ac.jp/topics/llm-jp-4-1/)
- [LLM-jp-3 MoE シリーズの公開](https://llm-jp.nii.ac.jp/blog/blog-603/)
- [LLM-jp: A Cross-organizational Project (arXiv:2407.03963)](https://arxiv.org/abs/2407.03963)
- [llm-jp-eval](https://github.com/llm-jp/llm-jp-eval)
- [llm-jp GitHub 組織](https://github.com/llm-jp)
- [llm-jp-3-13b](https://huggingface.co/llm-jp/llm-jp-3-13b)
- [llm-jp-4-8b-instruct](https://huggingface.co/llm-jp/llm-jp-4-8b-instruct)
- [llm-jp-4-32b-a3b-thinking](https://huggingface.co/llm-jp/llm-jp-4-32b-a3b-thinking)
