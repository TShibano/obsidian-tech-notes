---
title: "LLMキャラクター"
date: 2026-10-05
tags:
  - AI
  - LLM
  - Agent
  - NLP
related:
  - "[[LoRA]]"
  - "[[RAG]]"
  - "[[LLM実行環境]]"
  - "[[Ollama]]"
  - "[[LMStudio]]"
  - "[[llm-jp]]"
  - "[[ベクトルDB]]"
---

## 概要

LLM にキャラクター（人格・口調・設定）を演じさせる技術の総称．研究上は Role-Playing Language Agents（RPLA）と呼ばれ，システムプロンプトやキャラクターカードによるペルソナ設計，ファインチューニング，長期記憶，評価，安全性が主な論点になる．

## 詳細

### 研究上の位置づけ

- サーベイ "From Persona to Personalization"（Chen ら，TMLR 2024）は，ペルソナを Demographic Persona（統計的属性），Character Persona（歴史上・架空・公人など確立した人物），Individualized Persona（ユーザーとの対話で個別化）の3種に分類する．用途として感情支援チャットボット，ゲーム，パーソナルアシスタント，故人・本人のデジタルアバターを挙げる．
- 2026年1月のサーベイ（Wang ら）は，技術の進展をルールベースのテンプレート，言語スタイルの模倣，性格と記憶を重視した認知シミュレーションの3段階で整理している．評価軸はキャラクター知識の保持，性格の一貫性，価値観の整合，ハルシネーション耐性．

### 実装アプローチ

1. プロンプトによるペルソナ設計: システムプロンプトに設定・口調・例文を記述する．最も手軽で，クラウド API でもローカルでも使える．
2. キャラクターカード（SillyTavern 等）: 名前，Description（背景・外見・性格），Personality Summary，Scenario，First Message，Examples of dialogue，Alternate Greetings といったフィールドでキャラクターを定義する．SillyTavern のドキュメントは，キャラクター定義がトークンを消費し，コンテキストが小さいモデルでは記憶領域を圧迫する点を注意している．
3. ファインチューニング: 
   - Character-LLM（Shao ら，EMNLP 2023）は，キャラクターのプロフィール・経験・感情状態を使って LLM を訓練し，ベートーヴェンやクレオパトラ，カエサルなどを再現する．プロンプトベースからパラメータレベルへの移行を狙う．
   - RoleLLM（Wang ら）は，Role Profile Construction，Context-Instruct，RoleGPT，Role-Conditioned Instruction Tuning（RoCIT）の4段階で構成され，100ロール・168,093サンプルの RoleBench と，RoleLLaMA（英語）・RoleGLM（中国語）を作る．
   - [[LoRA]] などの軽量な微調整で口調を付与する方法もある．
4. 長期記憶: Generative Agents（Park ら，2023）は，経験を自然言語で記録する memory stream，高次の reflection への統合，記憶の動的検索による planning を組み合わせ，25体のエージェントが自律的にバレンタインパーティーを企画する様子を示した．実装では [[RAG]] や [[ベクトルDB]] による会話履歴・設定の検索で近いことを行う．

### 評価

- CharacterEval（Tu ら，2024）: 中国語．小説・脚本由来の77キャラクター，1,785の複数ターン対話（23,020例），4次元13指標，報酬モデル CharacterRM を併用．中国語モデルは中国語ロールプレイで GPT-4 より有望という結果を報告している．
- RoleBench: 上記 RoleLLM の評価データ．
- InCharacter（Wang ら，ACL 2024）: 心理尺度を用いたインタビューで性格の忠実度を測る．32キャラクター・14尺度で評価し，最先端の RPA の性格は人間が認知するキャラクター性格と高く整合し，精度は最大80.7%と報告．
- Japanese-RP-Bench（Aratako）: 日本語．10ターンのロールプレイ対話を生成し，Roleplay Adherence，Consistency，Contextual Understanding，Expressiveness，Creativity，Naturalness of Japanese，Enjoyment，Turn-Taking の8基準を複数の審査モデル（GPT-4o，o1-mini，Claude 3.5 Sonnet，Gemini 1.5 Pro）の平均で採点する．

### 代表的なサービスとローカル実装

- Character.AI: ユーザーが作ったキャラクターと対話するサービス．後述の安全性問題で未成年向け方針を大きく変更した．
- SillyTavern: キャラクターカードを使うローカル／セルフホスト向けのフロントエンド．バックエンドとして [[Ollama]] や [[LMStudio]] などの [[LLM実行環境]] を接続して使う想定（接続方法の詳細は未確認）．
- [[Claude Model]] や [[GPT Model]] などの商用モデルはシステムプロンプトでペルソナを与えられるが，安全性方針の範囲内で動作する．

### 日本語キャラクター対話

- Aratako 氏が Hugging Face に日本語ロールプレイ用モデル（Ninja-v1-RP，Oumuamua-7b-RP，Japanese-Starling-ChatV-7B-RP，NemoAurora-RP-12B など）と合成ロールプレイデータセットを公開している（各モデルの学習手法は一次情報で未確認）．
- 日本語の国産 LLM（[[llm-jp]] など）をベースにした口調付与も考えられるが，具体事例は未調査．

### 一貫性と安全性

- ペルソナドリフト: Anthropic の "The Assistant Axis"（2026-01）は，モデルが会話中に Assistant 的なペルソナから逸脱する方向（Assistant Axis）を活性化空間に見いだした．ユーザーが感情的な脆弱性を見せるセラピー的な会話や，モデル自身の本性を問う哲学的な議論で，Assistant から徐々にドリフトする．Gemma 2 27B，Qwen 3 32B，Llama 3.3 70B で275のキャラクター原型を抽出し，活性化の範囲を制限する activation capping により有害な応答を約50%減らしつつ能力を保ったと報告している．
- 悪役の演技: "Too Good to be Bad"（2025）は，Moral RolePlay ベンチマークで，キャラクターの道徳性が下がるほど演技の忠実度が単調に低下し，安全性アラインメントが強いモデルほど悪役演技が苦手になると報告している．
- Character.AI は 2025-10-29，報道や規制当局・安全の専門家・保護者からの指摘を受けて，2025-11-25 までに18歳未満のオープンエンドなチャットを廃止すると発表した（移行期間中は1日あたりの利用時間を段階的に制限）．なお同社は未成年の自殺をめぐる訴訟も抱えていたと報じられているが，発表自体は訴訟に言及していない．年齢確認技術（自社モデル + Persona 等）と，独立非営利の AI Safety Lab の設立も発表している．
- 依存・感情的な愛着，年齢確認，実在人物の再現（IP・肖像権）が横断的な課題．

## ポイント

- キャラクター付与は「プロンプト → キャラクターカード → 微調整 → 記憶・エージェント化」の順に深くなる．まずプロンプトで試し，口調の安定が必要なら [[LoRA]] を検討する．
- 評価は知識・口調・性格・一貫性など多次元．LLM 審査員は複数モデルの平均が人手評価との相関が高いという報告がある（Japanese-RP-Bench）．
- 感情的・内省的な会話ではペルソナがドリフトしやすい．設定の再注入や記憶検索で補う．
- 安全性対策（特に未成年・メンタルヘルス）が事業上の前提になっている．

## 関連項目

- [[LoRA]]
- [[RAG]]
- [[ベクトルDB]]
- [[LLM実行環境]]
- [[Ollama]]
- [[LMStudio]]
- [[llm-jp]]

## 参考

- [From Persona to Personalization: A Survey on Role-Playing Language Agents](https://arxiv.org/abs/2404.18231)
- [Role-Playing Agents Driven by Large Language Models: Current Status, Challenges, and Future Trends](https://arxiv.org/abs/2601.10122)
- [Character-LLM: A Trainable Agent for Role-Playing](https://arxiv.org/abs/2310.10158)
- [RoleLLM](https://arxiv.org/abs/2310.00746)
- [CharacterEval](https://arxiv.org/abs/2401.01275)
- [InCharacter](https://arxiv.org/abs/2310.17976)
- [Generative Agents](https://arxiv.org/abs/2304.03442)
- [Too Good to be Bad: On the Failure of LLMs to Role-Play Villains](https://arxiv.org/abs/2511.04962)
- [The assistant axis (Anthropic)](https://www.anthropic.com/research/assistant-axis)
- [Japanese-RP-Bench](https://github.com/Aratako/Japanese-RP-Bench)
- [SillyTavern Docs: Character Design](https://docs.sillytavern.app/usage/core-concepts/characterdesign/)
- [Character.AI: u18 chat announcement](https://blog.character.ai/u18-chat-announcement/)
