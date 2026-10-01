---
title: "Jev"
date: 2026-10-01
tags:
  - AI
  - ML
  - Agent
related:
  - "[[Nimble]]"
  - "[[ハーネスエンジニアリング]]"
  - "[[Claude Code]]"
---

## 概要

Jev は TypeSafe AI が2026年9月に発表した「System One Model」である．テキストを一切生成せず，入力された状態（state）と型付きの質問に対して，**型付きの判定と較正された確率**だけを返す．LLM と比べて桁違いに速く安いことをうたい，ソフトウェアから呼び出される判定部品として設計されている．

## 詳細

### System One という考え方

名前は Daniel Kahneman の「システム1（速い直感）/ システム2（遅い熟考）」に由来する．TypeSafe AI は，人間の好みに合わせて RLHF で調整された LLM は過信やモード崩壊を起こしやすく，人間の監視が必要なチャットボットになったと主張する．Jev は「人と話す AI」ではなく「ソフトウェアと話す AI」を目指している．

- 文章，コードブロック，拒否文を書くことはできない
- 代わりに，判定と確率を返す．結果は型安全で，パース不要
- 学習には RLHF ではなく **RLCD（Reinforcement Learning for Calibrated Decisions）** を使う（アーキテクチャは非公開）

### 質問の型

| 型 | 内容 | 返り値 |
|----|------|--------|
| **Noul** | Yes / No（真偽） | 真である確率 |
| **Choice** | N 個の選択肢から1つ | 各選択肢の確率 |
| **Score** | 低・中・高などの順序付き尺度 | スコアの分布と信頼度 |

1リクエストに複数の質問を入れると並列に評価され，質問を増やしても応答時間はほとんど変わらない．

### API の例

```json
// リクエスト
{
  "model": "jev-latest",
  "state": "Hi, I've been trying to connect my Stripe account for 3 days...",
  "questions": {
    "is_urgent": {
      "type": "noul",
      "instructions": "The message conveys urgency or time-sensitivity"
    }
  }
}

// レスポンス
{
  "is_urgent": { "type": "noul", "noul": 0.999 }
}
```

LangChain からも使える．

```python
from langchain_typesafe import Noul, TypeSafeClassifier

classifier = TypeSafeClassifier()
response = classifier.invoke({
    "state": "The deploy failed twice and customers are seeing 500s...",
    "questions": {
        "urgent": Noul(instructions="Does this need attention right now?"),
    },
})
urgency = response.nouls["urgent"].noul
```

### 性能と価格（ベンダー発表値）

| 項目 | 値 |
|------|-----|
| 応答時間 | 70〜500 ms（エンドツーエンド） |
| 速度 | フロンティア LLM 比 40〜200 倍高速（最大 193.6 倍） |
| コスト | フロンティア LLM 比 40〜400 倍安価（最大 444.6 倍） |
| 価格 | 入力 100 万トークンあたり $0.042．出力トークンは無料 |

### 注意点

- ベンチマークはベンダー自身が実施したもの
- TypeSafe 自身，価格が補助金的でないことは証明できないとしている
- 「ハルシネーションゼロ」は出力がスキーマに必ず従うという意味であり，判定の正確さを保証するものではない
- 現時点ではウェイトリスト付きのホスト API（アーリーアクセス）のみで，重みやセルフホストは提供されていない

### ユースケース

- **モデルルーティング**: リクエストが単純か複雑かを判定し，安いモデルと高性能モデルを振り分ける
- **ガードレール**: エージェントのツール実行（bash など）の危険度を判定し，危ないものをブロックまたは人間にエスカレーションする．[[Claude Code]] の auto mode に似た仕組みをオープンに作れる
- **分類・トリアージ**: 問い合わせの緊急度，カテゴリ分け
- **確率による自動化の分岐**: 確率が閾値以上なら自動実行，それ未満なら人間に回す

[[ハーネスエンジニアリング]]の文脈では，ハーネス内の判定（ルーティング，検証，ガードレール）を LLM に文章で答えさせる代わりに，Jev のような判定モデルで高速・安価・型安全に行うという使い方が想定されている．

### オープンな追随

発表直後に Bespoke Labs が，Jev に着想を得たオープンな判定モデル [[Nimble]]（9B）を公開した．社内評価で Jev 1.13.0 の 93.2% に対し 90.1% の一致率を出している．

## ポイント

- テキストを生成せず，型付きの判定と確率だけを返す「System One Model」
- 質問の型は Noul（真偽），Choice（選択），Score（尺度）の3種類
- 確率が較正されているので，閾値で自動実行とエスカレーションを切り替えられる
- 複数の質問を並列に評価でき，速度・コストで LLM に大差をつけるとされる
- 数値はベンダー発表値であり，独立した検証はまだ少ない
- エージェントのハーネスにおけるルーティング・ガードレール用の部品として有望

## 関連項目

- [[Nimble]] — Jev に着想を得たオープンな 9B 判定モデル
- [[ハーネスエンジニアリング]] — ハーネス内の判定部品として Jev が使われる
- [[Claude Code]] — ツール実行の承認判定（auto mode）という同種の課題を持つエージェント
- [[LLM]]
- [[RLHF]]

## 参考

- [TypeSafe AI 公式サイト](https://typesafe.ai/)
- [What Is Jev? Building a harness with Jev | LangChain Blog](https://www.langchain.com/blog/building-a-harness-with-jev)
- [TypeSafe AI Releases Jev | MarkTechPost](https://www.marktechpost.com/2026/09/19/typesafe-ai-releases-jev/)
- [Jev on Fly.io Sprites: typed decisions for your agents | Fly.io](https://fly.io/sprites/jev/)
