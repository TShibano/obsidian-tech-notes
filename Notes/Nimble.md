---
title: "Nimble"
date: 2026-10-01
tags:
  - AI
  - ML
  - LLM
related:
  - "[[Jev]]"
  - "[[ハーネスエンジニアリング]]"
  - "[[Ollama]]"
---

## 概要

Nimble（Bespoke-Nimble-9B）は Bespoke Labs が2026年9月に公開したオープンな判定モデルである．テキストとスキーマを受け取り，各質問について**選んだ答えと全候補の確率**を型付きで返す．TypeSafe AI の [[Jev]] に着想を得ており，Qwen3.5-9B を LoRA でファインチューニングしたモデルで，ローカルで動かせる．

## 詳細

### 何をするモデルか

- 入力: テキストと，フラットなスキーマ（質問のリスト）
- 出力: 質問ごとの選択結果，各候補の確率（logits を softmax したもの），生の logits
- 推論の文章を先に書かないので速い．JSON も生成しないのでパースの失敗がない

スキーマで使えるフィールドは2種類．

| 型 | 内容 |
|----|------|
| `enum` | 固定の文字列候補から1つを選ぶ（1〜255個） |
| `boolean` | true / false |

各フィールドと各候補には説明文を付けられる．

### 仕組み

答えの候補をそれぞれ語彙の1トークンに対応させ，そのトークンに対するモデルの logits を読み取る．softmax で確率分布にし，Python 側で型付きのレスポンスを組み立てる．テキスト生成を経ないため，出力がスキーマから外れることがない．

### 学習手法：Contrastive Data Curation

確率付きのラベルを集める代わりに，**1つの事実だけを変えて正解が反転する「ほぼ同一の2例」のペア**を作って学習させる．モデルは「どの証拠が判定を変えるべきか」を学ばされる．

1. **ルール確認**: スキーマから判定の根拠となる事実を取り出し，異なる答えが導けるか確認
2. **対比ペアの構築**: ほぼ同じ2例を作り，焦点となる1つの事実だけを変えて答えを反転させる
3. **両例の検証**: 別々のモデル呼び出しで事実を確認し，一貫性と証拠の必要性を検証
4. **ラベル生成**: コードでルールを適用してラベルを付け，すべての条件を満たすペアだけを残す

- 学習データ: 10カテゴリから合成した **2,676例**（3種類のタスクが混在）
- Qwen3.5-9B に対する LoRA ファインチューニング．答えのトークンにのみ損失をかけ，1エポック
- 開発期間は約2日

### 評価（Bespoke Labs の社内ホールドアウト 324例）

| モデル | 参照ラベルとの一致率 |
|--------|----------------------|
| Jev 1.13.0 | 93.2% |
| **Bespoke-Nimble-9B** | **90.1%** |
| Qwen3.5-9B（未調整） | 66.4% |

推論レイテンシは H100 で中央値 106 ms / 例．なお評価セットは Bespoke Labs 自身のものである．

### リリース経緯

- 2026-09-18: 初版公開（公開ベンチマーク付き）
- 2026-09-22: 温度による確率の較正（temperature calibration）を追加
- 2026-09-24: コンテキスト長 8,192 トークン対応のチェックポイント（学習時は 2,048）

Jev の蒸留ではなく独立に開発されている．重みは LoRA アダプタ形式で Hugging Face に公開されており，マージして使える．

### 使い方

```python
from pathlib import Path
from huggingface_hub import snapshot_download

snapshot = Path(snapshot_download("bespokelabs/Bespoke-Nimble-9B"))
```

```python
# Mac（MLX）でのスコアリング例
import json
from pathlib import Path
from nimble.scoring.parallel_scorer import ParallelScorer

config = json.loads(Path(".cache/nimble-model.json").read_text())
scorer = ParallelScorer(**config)

schema = {"priority": {"type": "enum", "choices": ["HIGH", "LOW"]}}
result = scorer.score("本番環境で 500 エラーが多発している", schema)
print(result["output"])  # {"priority": "HIGH"}
```

### Jev との比較

| 観点 | [[Jev]] | Nimble |
|------|---------|--------|
| 提供形態 | ホスト API（ウェイトリスト） | オープンウェイト，ローカル実行 |
| モデル | 非公開（RLCD で学習） | Qwen3.5-9B + LoRA |
| 質問の型 | Noul / Choice / Score | boolean / enum |
| 学習手法 | 非公開 | Contrastive Data Curation（レシピ公開） |
| 社内評価の一致率 | 93.2% | 90.1% |

### ユースケース

- **ルーティング**: 行き先と選択条件を宣言し，選ばれた行き先とその確率を受け取る
- **分類・トリアージ**: 優先度，カテゴリ，可否判定
- エージェントの[[ハーネスエンジニアリング]]における判定部品を，外部 API に出さずローカルで動かしたい場合

## ポイント

- Jev 型の「型付き判定モデル」を誰でも作れるオープンなレシピとして公開
- 候補を1トークンに対応させ logits を読むので，生成もパースも不要
- 1つの事実だけ変えて答えが反転するペアで学習する Contrastive Data Curation が肝
- 2,676例・約2日の LoRA 学習で，社内評価では Jev に3ポイント差まで迫った
- 評価は Bespoke Labs 自身のデータセットであり，汎用性は今後の検証待ち

## 関連項目

- [[Jev]] — Nimble が着想を得た TypeSafe AI の判定モデル
- [[ハーネスエンジニアリング]] — エージェントのルーティング・ガードレールでの利用
- [[Ollama]] — ローカルでモデルを動かすランタイム
- [[LoRA]]
- [[Qwen]]

## 参考

- [GitHub - bespokelabsai/nimble](https://github.com/bespokelabsai/nimble)
- [bespokelabs/Bespoke-Nimble-9B | Hugging Face](https://huggingface.co/bespokelabs/Bespoke-Nimble-9B)
