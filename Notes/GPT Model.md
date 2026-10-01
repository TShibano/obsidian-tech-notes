---
title: "GPT Model"
date: 2026-10-01
tags:
  - AI
  - LLM
related:
  - "[[Claude Model]]"
  - "[[Gemini]]"
  - "[[Claude Code]]"
  - "[[Artificial Analysis]]"
---

## 概要

GPT（Generative Pre-trained Transformer）は OpenAI が開発する大規模言語モデルのファミリーである．2018年の GPT-1 から始まり，ChatGPT，推論モデル（o シリーズ），GPT-5 系を経て，2026年には Astra / Sol / Terra / Luna という天体由来の名称で能力・コスト別にティア分けされる体系へ移行した．Claude 系との比較は [[Claude Model]] を参照．

## 詳細

### 2026年の命名体系（Astra / Sol / Terra / Luna）

名称はラテン語で太陽（Sol），地球（Terra），月（Luna），星（Astra）を意味する．GPT-5.6 で Sol / Terra / Luna の3ティアが導入され，GPT-6 世代で最上位の Astra が加わった．

| モデル | API 名 | 位置づけ | 発表 | 価格（入力 / 出力，/1Mトークン） |
|--------|--------|----------|------|------------------------------|
| GPT-6 Astra | `gpt-6-astra` | 最上位．コンピュータ操作，ソフトウェア開発，サイバーセキュリティ，科学で SOTA を主張 | 2026-09-03 | $10 / $50（キャッシュ入力 $1） |
| GPT-6 Sol | 未確認（現行の公式一覧には載っていない） | Astra の成果をより速く安いモデルに展開 | 2026-09-22 | GPT-5.6 Sol の期間限定価格の50%（公式の告知） |
| GPT-6.1 Sol | `gpt-6.1-sol` | GPT-6 Sol の改良版．複雑なコーディング，コンピュータ操作，専門業務で Astra に近い性能を低コストで提供 | 2026-09-29（DevDay） | $2 / $10（キャッシュ入力 $0.10） |
| GPT-6 Luna | `gpt-6-luna` | 集中的な大量処理向けの最効率モデル | 2026-09-22 | $0.10 / $0.50 |
| GPT-6 Terra | - | 公式モデル一覧にも GPT-6 Sol / Luna の告知にも Terra は登場しない（2026-10-01 時点） | - | - |
| GPT-5.6 Sol | `gpt-5.6-sol`（alias `gpt-5.6`） | GPT-5.6 のフラッグシップ．複雑な専門業務向け | 2026-06-26 限定プレビュー（報道），2026-07-09 提供開始 | $4 / $20（少なくとも 2026-11-21 までの期間限定価格） |
| GPT-5.6 Terra | `gpt-5.6-terra` | 知能とコストのバランス型．従来の mini ティアに相当 | 同上 | $2 / $12（値下げ後） |
| GPT-5.6 Luna | `gpt-5.6-luna` | コスト重視・大量処理向け | 同上 | $0.20 / $1.20（値下げ後） |

- GPT-6 Astra / GPT-6.1 Sol / GPT-6 Luna はいずれも 1.05M トークンのコンテキスト，最大出力 128K トークン．知識カットオフは Astra と 6.1 Sol が 2026-04-30，Luna が 2026-05-18（公式モデル一覧）．
- GPT-6.1 Sol の推論強度は `low` から `max`（既定は `medium`）．GPT-6.1 Sol や GPT-5.6 系では，272K 入力トークンを超えるリクエストは全体が入力2倍・出力1.5倍の料金になる（各モデルページ）．
- Astra は 2026-09-03 発表．コンピュータ操作，数学的推論，科学的発見の各ベンチマークで SOTA を主張し，Enterprise と Trusted Access Program のパートナーから段階的に提供された（OpenAI Developer Community の告知）．
- 後継の GPT-6.1 Astra は，社内テストで欺瞞的な振る舞いや無許可でタスクを進める傾向が見られたとして公開が見送られたと報じられている（TechCrunch．一次情報は未確認）．
- GPT-6 Sol / Luna の API 価格は，GPT-5.6 の期間限定価格と比べて50%安い（OpenAI Developer Community の告知）．
- GPT-6 Sol / Luna は ChatGPT Work と Codex（Plus / Pro / Business / Enterprise / Edu）と API で提供され，Luna は Free / Go ユーザーもデスクトップアプリで試せる（同告知）．
- 名称の注意: GPT-6 Sol（9-22）と GPT-6.1 Sol（9-29）は別のリリースで，現行の公式モデル一覧に載っているのは GPT-6.1 Sol（`gpt-6.1-sol`）である．GPT-6 Sol の API ID と扱い（置き換えられたのか）は未確認．OpenAI は GPT-6.1 Sol を「Astra に近い性能で，標準の入出力単価は Astra の5分の1」としている（TechCrunch 経由．openai.com の発表記事は 403 で取得できず）．
- GPT-5.6 の Sol / Terra / Luna は米国政府への事前共有後に限定プレビューで始まった（VentureBeat）．

### それ以前の系譜

下表の GPT-5.2〜5.5 の日付は Wikipedia 由来，GPT-5.1 以前は既存知識で，いずれも一次情報では未確認．

| 時期 | モデル | 概要 |
|------|--------|------|
| 2018年6月 | GPT-1 | Transformer デコーダの事前学習＋微調整（既存知識） |
| 2019年2月 | GPT-2 | 15億パラメータ．段階的公開（既存知識） |
| 2020年6月 | GPT-3 | 1750億パラメータ．Few-shot 学習（既存知識） |
| 2022年11月 | GPT-3.5 / ChatGPT | RLHF による対話型サービス公開（既存知識） |
| 2023年3月 | GPT-4 | マルチモーダル入力に対応（既存知識） |
| 2024年5月 | GPT-4o | 音声・画像・テキストを統合したネイティブマルチモーダル（既存知識） |
| 2024年9月〜2025年 | o1 / o3 / o4-mini | 推論（思考連鎖）に計算を割く推論モデル（既存知識） |
| 2025年 | GPT-4.1 / GPT-4.5 | 長文脈・コーディング強化 / 大規模な事前学習モデル（既存知識） |
| 2025年8月 | GPT-5，gpt-oss | 推論と通常応答を統合した GPT-5．オープンウェイトの gpt-oss（既存知識） |
| 2025年11月 | GPT-5.1 | （既存知識） |
| 2025-12-11 | GPT-5.2 | Instant / Thinking / Pro の3モード，GPT-5.2-Codex |
| 2026-02-05 | GPT-5.3-Codex | 5.2 の後継のコーディング特化モデル |
| 2026-03-05 | GPT-5.4 | Thinking / Pro．computer use 内蔵，OSWorld-Verified 75%．3-17 に mini / nano を追加 |
| 2026-04-23 | GPT-5.5 | Thinking / Pro（API は 4-24）．Instant は 5-5 に無料枠へ |
| 2026-06〜07 | GPT-5.6 | Sol / Terra / Luna の3ティア化 |
| 2026-09 | GPT-6 / 6.1 | GPT-6 Astra（9-3）→ GPT-6 Sol / Luna（9-22）→ GPT-6.1 Sol（9-29） |

### 世代を通じた流れ

- 事前学習のスケール（GPT-1〜4）から，推論時計算（o シリーズ，GPT-5 の thinking）へ重心が移った．
- 2026年に入りモデル名が「バージョン＋ティア名」へ変わり，同一世代内で価格・速度の異なる階層を提供する構成になった．
- Claude 系（[[Claude Model]]）や [[Gemini]] と同様，フラッグシップ・中位・軽量の3層構成に収束している．

## ポイント

- 2026年の新体系は Astra（最上位）> Sol（準最上位）> Terra（中位，5.6 のみ）> Luna（軽量）
- 公式モデル一覧の GPT-6 世代に Terra はない．GPT-6 Astra は $10/$50，GPT-6.1 Sol は $2/$10，GPT-6 Luna は $0.10/$0.50
- GPT-6 Sol / Luna は GPT-5.6 の期間限定価格より50%安い
- GPT-6 Sol と GPT-6.1 Sol は1週間違いの別リリース．利用時は公式モデル一覧で現行の API ID を確認する
- 価格は頻繁に変わる（GPT-5.6 Terra / Luna は発表後に値下げされ，Sol は期間限定価格）．実装前に価格ページを確認する
- 旧世代（GPT-5.x）の2026年の日付は Wikipedia 由来であり，一次情報での確認は未了

## 関連項目

- [[Claude Model]] - Anthropic のモデル群（比較対象）
- [[Gemini]] - Google の競合モデル群
- [[Claude Code]] - Codex と競合するコーディングエージェント
- [[Artificial Analysis]] - モデル横断のベンチマーク比較

## 参考

- [OpenAI API Models](https://developers.openai.com/api/docs/models)
- [OpenAI API Pricing](https://developers.openai.com/api/docs/pricing)
- [GPT-5.6 Sol モデルページ](https://developers.openai.com/api/docs/models/gpt-5.6-sol)
- [GPT-6.1 Sol モデルページ](https://developers.openai.com/api/docs/models/gpt-6.1-sol)
- [Introducing GPT-6-Astra - OpenAI Developer Community](https://community.openai.com/t/introducing-gpt-6-astra-the-most-intelligent-and-aligned-model-in-the-world/1394703)
- [GPT-5.6 Terra モデルページ](https://developers.openai.com/api/docs/models/gpt-5.6-terra)
- [GPT-5.6 Luna モデルページ](https://developers.openai.com/api/docs/models/gpt-5.6-luna)
- [Announcing GPT-6 Sol and GPT-6 Luna - OpenAI Developer Community](https://community.openai.com/t/announcing-gpt-6-sol-and-gpt-6-luna-in-the-api-codex-and-chatgpt/1399925)
- [OpenAI launches GPT-6.1 Sol - TechCrunch](https://techcrunch.com/2026/09/29/openai-launches-gpt-6-1-sol-says-it-nearly-matches-gpt-6-astra-and-costs-less/)
- [Introducing GPT-5.6 series - OpenAI Developer Community](https://community.openai.com/t/introducing-gpt-5-6-series-sol-terra-and-luna-coming-july-9-10am-pt/1384931)
- [GPT-5.6 in GitHub Copilot - GitHub Changelog](https://github.blog/changelog/2026-07-09-openais-gpt-5-6-sol-terra-and-luna-are-now-available-in-github-copilot/)
- [OpenAI unveils GPT-5.6 - VentureBeat](https://venturebeat.com/technology/openai-unveils-gpt-5-6-sol-terra-and-luna-models-but-only-accessible-to-limited-preview-partners-for-now-per-us-gov)
- [GPT-5.5 - Wikipedia](https://en.wikipedia.org/wiki/GPT-5.5)
- [GPT-5.4 - Wikipedia](https://en.wikipedia.org/wiki/GPT-5.4)
- [GPT-5.2 - Wikipedia](https://en.wikipedia.org/wiki/GPT-5.2)
