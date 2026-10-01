---
title: "Claude Model"
date: 2026-10-01
tags:
  - AI
  - LLM
related:
  - "[[Claude Mythos]]"
  - "[[Claude Code]]"
  - "[[LLM]]"
  - "[[GPT Model]]"
  - "[[Gemini]]"
  - "[[サブエージェント]]"
---

## 概要

Claude は Anthropic の LLM ファミリーで，2026-10-01 時点では Mythos / Fable，Opus，Sonnet，Haiku のティアで構成される．Fable と Mythos は同一の基盤モデルで安全装置の強さだけが異なり，Fable が一般提供，Mythos は招待制の限定提供となっている．

## 詳細

### ティアの位置づけ

| ティア | 位置づけ | 提供形態 |
|--------|----------|----------|
| Mythos | 最上位．Fable と同一モデルで，認可されたサイバーセキュリティ・研究用途向けに安全装置を外したもの | Project Glasswing 参加者など trusted access のみ（招待制） |
| Fable | 全顧客に開放されたモデルの中で最高能力．長時間のエージェント作業や高難度の推論向け | 一般提供（Claude API，AWS，Google Cloud，Microsoft Foundry） |
| Opus | 長時間の自律コーディング・ナレッジワーク向け．公式ドキュメントでは「多くのワークロードの出発点」 | 一般提供 |
| Sonnet | 速度と知能のバランス．日常的なコーディング・エージェント・業務向け | 一般提供 |
| Haiku | 最速・最安．リアルタイム処理，大量処理，サブエージェント向け | 一般提供 |

- Mythos の初代 Preview（2026-04-07 発表）は Opus より上の新階層として登場した．経緯，サイバーセキュリティ能力，Project Glasswing の詳細は [[Claude Mythos]] を参照．
- Mythos Preview は 2026-06-09 に deprecated となった（退職日は未発表）．同日に Fable 5 / Mythos 5 が発表され，価格は入力 $10 / 出力 $50 per MTok で Mythos Preview の半分未満とされた．
- Fable 5 では，分類器がサイバーセキュリティ，生物・化学，蒸留に関わるリクエストを検出すると，応答を自動的に Opus 4.8 が処理する．Anthropic は「Fable のセッションの95%超でフォールバックは発生せず，その場合の性能は Mythos 5 と実質同じ」としている．

### 現行モデル（2026-10-01 時点）

| 項目 | Fable 5.1 | Opus 5.5 | Sonnet 5.5 | Haiku 4.5 |
|------|-----------|----------|------------|-----------|
| API ID | `claude-fable-5-1` | `claude-opus-5-5` | `claude-sonnet-5-5` | `claude-haiku-4-5-20251001`（alias: `claude-haiku-4-5`） |
| 入力 / 出力（$ / MTok） | 10 / 50 | 4 / 20 | 2 / 10 | 1 / 5 |
| キャッシュ読み出し（$ / MTok） | 0.25 | 0.20 | 0.20 | 0.10 |
| Batch API（入力 / 出力） | 5 / 25 | 2 / 10 | 1 / 5 | 0.50 / 2.50 |
| コンテキスト長 | 1M | 1M | 1M | 200K |
| 最大出力 | 128K | 128K | 128K | 64K |
| 思考方式 | Adaptive（常時オン） | Adaptive（常時オン） | Adaptive | Extended |
| デフォルト effort | high | medium | high | 非対応 |
| 知識カットオフ（reliable） | 2026-06 | 2026-06 | 2026-06 | 2025-02 |
| 相対レイテンシ | 遅い | 中 | 速い | 最速 |
| 退職予定（早くとも） | 2027-09-01 | 2027-09-22 | 2027-09-28 | 2026-10-15 |
| 発表日 | 2026-09（退職予定日から 09-01 と推定） | 2026-09-22 | 2026-09-28 | 2025-10-15（既存知識） |

- Mythos 5.1（`claude-mythos-5-1`）は Fable 5.1 と同価格・同能力で，Project Glasswing 参加者限定．
- Bedrock ID は `anthropic.claude-<name>`（例: `anthropic.claude-opus-5-5`）．Google Cloud ID は Fable / Opus / Sonnet が API ID と同じで，Haiku 4.5 のみ `claude-haiku-4-5@20251001`．
- Haiku 4.5 は退職が「2026-10-15 より早くない」と近く，移行先の検討が必要．Sonnet 4.5 は 2026-09-30 に deprecated（2026-11-30 退職，移行先 `claude-sonnet-5-5`）．
- Fable 5.1 は Fable 5 比で典型的ワークロードのコストが約25%減，エージェント的な作業では最大45%減．入出力単価は据え置きで，キャッシュ読み出しが $1 から $0.25 / MTok（基本入力価格の 2.5%）に下がった．Terminal-Bench-Science 0.1 は 52.6%（Fable 5 は 24.7%）．いずれも Anthropic の主張．
- Opus 5.5 は Claude 5.5 ファミリーの最初のモデルで，Anthropic は「ほとんどの作業で Fable 5.1 並みの性能，Opus 5 より実行コストが40%低い」としている．単価は Opus 5 の $5 / $25 から $4 / $20 に下がり，キャッシュ読み出しは $0.50 から $0.20 になった．
- Sonnet 5.5 は単価が Sonnet 5 と同じ（$2 / $10）だが，同じ作業に必要なトークンが少なく，1タスクあたりのコストは Sonnet 5 比で最大30%低い．出力は Sonnet 5 より30%以上速い．いくつかのベンチマークでは Max effort の Sonnet 5.5 が Opus 5.5 に匹敵するが，長期的な判断を要する作業では Opus 5.5 のほうが明確に強いとされる．いずれも Anthropic の主張．
- Opus 5.5 / Opus 5 / Opus 4.8 は fast mode（研究プレビュー，最大2.5倍の出力速度）に対応．価格は Opus 5.5 が $8 / $40，Opus 5 と Opus 4.8 が $10 / $50 per MTok で，Claude API（ファーストパーティ）のみで使える．
- 4.7 以降は新トークナイザで同一テキストが約30%多くトークン化される．また 4.7 以降は `temperature` / `top_p` / `top_k` を非デフォルト値にすると 400 エラー．

### 旧モデル（still available）

Fable 5（$10 / $50，2026-06-09），Opus 5（$5 / $25，2026-07-24），Opus 4.8 / 4.7 / 4.6 / 4.5（各 $5 / $25），Sonnet 5（$2 / $10，2026-06-30），Sonnet 4.6（$3 / $15）．Sonnet 5 は導入価格 $2 / $10 がそのまま標準価格になった（予定されていた $3 / $15 への値上げは中止）．

### 世代の変遷

| 世代 | 主なモデル | 時期 | 備考 |
|------|-----------|------|------|
| Claude 1 / Instant | claude-1.x, claude-instant-1.x | 2023 | 2024-11-06 退職 |
| Claude 2 / 2.1 | claude-2.0, 2.1 | 2023 | 2025-07-21 退職 |
| Claude 3 | Haiku / Sonnet / Opus の3ティア体制 | 2024-03 | Sonnet 3 は 2025-07-21，Opus 3 は 2026-01-05，Haiku 3 は 2026-04-20 退職 |
| Claude 3.5 | Sonnet（2024-06，2024-10 更新），Haiku | 2024 | Sonnet 3.5 は 2025-10-28 退職，Haiku 3.5 は 2026-02-19 退職 |
| Claude 3.7 | Sonnet 3.7（claude-3-7-sonnet-20250219） | 2025-02 | 2026-02-19 退職 |
| Claude 4 / 4.1 | Opus 4 / Sonnet 4（2025-05-14 スナップショット），Opus 4.1（2025-08-05） | 2025 | Opus 4 / Sonnet 4 は 2026-06-15，Opus 4.1 は 2026-08-05 退職 |
| Claude 4.5 | Sonnet 4.5（2025-09-29），Haiku 4.5（2025-10-15），Opus 4.5（2025-11-24） | 2025 | Haiku 4.5 が現行 Haiku |
| Claude 4.6 - 4.8 | Opus 4.6（2026-02-05），Sonnet 4.6（2026-02-17），Opus 4.7（2026-04-16），Opus 4.8（2026-05-28） | 2026前半 | 日付は退職予定日からの推定（下記） |
| Mythos Preview | 限定公開 | 2026-04-07 | Opus より上位の新階層 |
| Claude 5 | Fable 5 / Mythos 5（2026-06-09），Sonnet 5（2026-06-30），Opus 5（2026-07-24） | 2026夏 | Fable 階層が新設．Opus 5 は $5 / $25 で Opus 4.8 と同価格 |
| Claude 5.1 / 5.5 | Fable 5.1 / Mythos 5.1（2026-09），Opus 5.5（2026-09-22），Sonnet 5.5（2026-09-28） | 2026-09 | 現行世代 |

- 現行・旧モデルの「退職予定（早くとも）」は，発表日を確認できたモデル（Fable 5，Sonnet 5，Opus 5，Opus 5.5，Sonnet 5.5）ではちょうど発表の1年後になっている．4.6 - 4.8 と Fable 5.1 の日付はこの対応からの推定で，公式発表では直接確認していない．
- Claude 1 から 4.5 の発表日は既存知識に基づく．モデル ID と退職日は公式 deprecations ページで確認済み．

### 選び方（公式ガイドの要約）

- 迷ったら Opus 5.5 から始め，`xhigh` や `max` の effort でも評価が足りない高難度の推論・長時間エージェント作業なら Fable 5.1 へ上げる．
- 効率優先なら Haiku 4.5 で試作し，足りない場合のみ上位へ．
- 低コストモデルとフロンティアモデルを組み合わせる構成も紹介されている．難しい判断だけを上位モデル（advisor）に相談する executor 型と，大量の作業を低コストの worker に委譲する orchestrator 型の2パターンがある（[[サブエージェント]] の考え方と対応）．
- effort パラメータの調整はモデル切り替えより有効なことが多い．

## ポイント

- ティアは Mythos / Fable > Opus > Sonnet > Haiku の順．Fable と Mythos は同一モデルで，違いは安全装置と提供範囲だけ．
- 世代番号はモデルごとに揃っていない．現行は Fable 5.1，Opus 5.5，Sonnet 5.5，Haiku 4.5．
- 価格は Fable $10/$50，Opus $4/$20，Sonnet $2/$10，Haiku $1/$5 per MTok．4.6 世代以降は 1M コンテキスト全体を標準価格で使える（長文脈の割増なし）．
- Opus 世代は 4.5 で $15/$75 から $5/$25 へ大幅値下げ，5.5 でさらに $4/$20 へ．
- モデル ID は 4.6 世代以降は日付なしでも固定スナップショット．
- 公開モデルの退職は最低60日前に通知され，最近は deprecated から退職まで約2か月．API ID の追従が必要．
- [[Claude Code]] などの利用モデルもこの表に沿って選ぶ．

## 関連項目

- [[Claude Mythos]] - Mythos Preview の経緯と Project Glasswing
- [[Claude Code]] - Claude モデルを使うエージェント型 CLI
- [[LLM]] - LLM 全般
- [[GPT Model]] - OpenAI 側のモデルファミリー
- [[Gemini]] - Google 側のモデル
- [[サブエージェント]] - 軽量モデルへの委譲

## 参考

- [Models overview - Claude Platform Docs](https://platform.claude.com/docs/en/about-claude/models/overview)
- [Pricing - Claude Platform Docs](https://platform.claude.com/docs/en/about-claude/pricing)
- [Model deprecations - Claude Platform Docs](https://platform.claude.com/docs/en/about-claude/model-deprecations)
- [Choosing the right model - Claude Platform Docs](https://platform.claude.com/docs/en/about-claude/models/choosing-a-model)
- [Introducing Claude Fable 5.1 and Claude Mythos 5.1 - Anthropic](https://www.anthropic.com/claude-fable-and-mythos-5-1)
- [Claude Fable 5 and Claude Mythos 5 - Anthropic](https://www.anthropic.com/news/claude-fable-5-mythos-5)
- [Claude Fable - Anthropic](https://www.anthropic.com/claude/fable)
- [Introducing Claude Opus 5.5 - Anthropic](https://www.anthropic.com/claude-opus-5-5)
- [Introducing Claude Sonnet 5.5 - Anthropic](https://www.anthropic.com/claude-sonnet-5-5)
- [Introducing Claude Opus 5 - Anthropic](https://www.anthropic.com/news/claude-opus-5)
