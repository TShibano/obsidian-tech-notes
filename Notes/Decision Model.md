---
title: "Decision Model"
date: 2026-10-01
tags:
  - AI
  - ML
  - LLM
related:
  - "[[Jev]]"
  - "[[Nimble]]"
  - "[[ハーネスエンジニアリング]]"
  - "[[LoRA]]"
  - "[[LLM]]"
  - "[[RLHF]]"
---

## 概要

Decision Model は，テキストを生成せず，**型付きの判定と各選択肢の確率**だけを返す AI モデルの系統である．TypeSafe AI は [[Jev]] をこの系統の最初のモデルとして「System One Model」と呼んでおり，発表直後から [[Nimble]]，Laya，Von，Kev などオープンな追随モデルが登場した．ソフトウェアから呼ばれる判定部品（ルーティング，ガードレール，トリアージ）という位置付けである．

## 詳細

### 共通するインターフェース

各モデルは「状態（テキストや JSON）」と「型付きの質問」を受け取り，質問ごとに選択結果と確率を返す．質問の型は Jev 由来の3種類（Choice / Score / Noul）か，Nimble の enum / boolean である．Kev，Von，Laya は Jev と同じ `/v1/systemone` 形式の API を話し，既存の Jev クライアントや TypeSafe の SDK を接続先の変更だけで使えると主張している．生成を経ないので，出力がスキーマから外れるパースエラーが原理的に起きない．

### モデルの比較

| モデル        | 提供元                | 構成                                                                     | 重み・ライセンス                                   | 発表者の主張                                                                                                          |
| ---------- | ------------------ | ---------------------------------------------------------------------- | ------------------------------------------ | --------------------------------------------------------------------------------------------------------------- |
| [[Jev]]    | TypeSafe AI        | 非公開（RLCD で学習）                                                          | 非公開，ホスト API                                | LLM 比で最大 193.6 倍高速・444.6 倍安価．入力 10 億トークン $42（100 万トークン $0.042）                                                  |
| [[Nimble]] | Bespoke Labs       | Qwen3.5-9B + [[LoRA]]（rank 16），候補トークンの logits を読む                      | 初版は Apache 2.0，v3 のアダプタは CC BY-NC 4.0（非商用） | 324 例の評価で Jev 93.21% に対し 90.12%，H100 で中央値 106 ms                                                                |
| Kev        | Jared Palmer       | Qwen3.5（0.8B / 4B / 9B）と Qwen3.8（27B）に rank-16 LoRA + 小さな pointer head | Apache 2.0                                 | 新規ソースで Kev-27B が 0.851（Jev 0.857），小型モデルは3〜4ポイント差．知識を要する MMLU-Pro では 0.675 対 0.840 と大きく劣る．H100 で 4B が6問を 18.1 ms |
| Laya       | Convai Innovations | ModernBERT-large（421M）に決定ヘッド．多言語版は mmBERT-base（322M）                   | Apache 2.0                                 | T4 で1問約 33 ms（バッチ時 1問あたり 7.2 ms）．4ワークフロー 2,000 判定の typed-decisions で 0.766（Jev 0.727）                           |
| Von        | wfzyx              | 395M の ModernBERT エンコーダ，非自己回帰                                          | Apache 2.0                                 | CPU（4 vCPU）で p50 0.34 秒，A10G で 0.023 秒．ただし JevBench v1.4 の総合は Von 27.5 に対し Jev 63.3                             |

設計は大きく2系統に分かれる．

- **デコーダ LLM を流用する系統**（Nimble，Kev）: 小型 LLM に LoRA を当て，答えトークンの確率を直接読む．言語理解の力を引き継げるが，モデルは 4B〜27B と大きい
- **エンコーダ + 決定ヘッドの系統**（Laya，Von）: ModernBERT など 数百 M のエンコーダで1回の forward pass で答える．CPU でも動くほど軽いが，Von のように精度は Jev に及ばない例もある

各社の数値は評価セットも指標も異なるため，横並びでは比較できない．

### 類似アプローチとの違い

| アプローチ | 違い |
|-----------|------|
| 従来の分類器（BERT 系など） | ラベル集合が学習時に固定．Decision Model は質問とその説明文を実行時に与えられる |
| 構造化出力（JSON モード，制約付きデコード） | 出力形式は守らせられるが，LLM が本文を生成するので遅く高い．確率も標準では得られない |
| LLM-as-judge | 推論文を書かせてから判定させる．Decision Model は文章を書かずに判定と確率だけ返す |
| Reward model | 応答の良し悪しを1つのスカラーで評価し，学習に使うのが主目的．Decision Model は任意の型付き質問に答え，実行時の分岐に使う |
| ガードレール専用モデル（Llama Guard，ShieldGemma，Qwen3Guard） | 安全性の固定タクソノミに特化．Llama Guard 系は先頭トークンの確率をスコアにできるが，Qwen3Guard はテキストで結果を返すため，トークン確率による閾値調整ができないという指摘がある |

ガードレール専用モデルと近いが，Decision Model は安全性に限らず，ルーティング・検索・トリアージなど任意の質問に使える汎用部品として売り出されている点が異なる．この対比は調査者による整理であり，各ベンダーが同じ比較を公式に示しているわけではない．

### 較正（calibration）の扱い

確率が信頼できることが価値の中心である．Jev は RLCD で「確信度が高いほど正解率が高い」ことを目指すとされる．Nimble は初版で温度スケーリング（T = 2.179）を後付けしたが，最新チェックポイントは T = 1.0 のまま使っている．Kev のリポジトリでは，新規ソースの質問で確信度 0.9 以上を誤答に付けた割合が Kev-9B 2.4%，Jev 3.7% と報告されている．いずれも発表者自身の評価である．

### 採用事例・アプリケーション事例

一次情報源で確認できたもののみ挙げる．確認できたのは，ベンダー公式の想定ユースケースと，個人開発者の公開リポジトリが中心で，企業の本番導入を裏付ける一次情報は見つからなかった．

**公式に示されたユースケース**

- LangChain: Jev を使ったミドルウェアを紹介している．リクエストを Jev に判定させて使うモデルを選ぶモデルルーティングと，ツール呼び出しのリスクを判定して実行前にブロックする AutoModeMiddleware
- Fly.io Sprites: タスクのルーティング（スクリプト / 高速エージェント / 推論エージェント / 人間），読み込むスキルの選択，サポートチケットのトリアージ（緊急度・担当チーム・顧客の苛立ち），認証・課金コードに触れる差分の人間レビュー要求
- TypeSafe 公式ドキュメント: モデルルーティング，LLM ガードレール，検索・リランキング，セマンティックなコード lint，サポート，保険請求，金融犯罪，モデレーションなど．業種別の例は「例」であり，導入実績ではない

**公開リポジトリで確認できた実装例**

- メールトリアージ（az9713/jev-email-triage）: 1通につき category，importance，brand-deal，scam の4問を Jev に投げ，README では1通あたり約 $0.00003 とされる
- 取引エージェント（jarrodwatts/jev-trader）: Monad ブロックチェーン上の Kuru の板で約 300 ms（1ブロック）ごとに，価格が動く方向を Jev に尋ねて指値を出す．モック（モメンタム）にも切り替えられる実験的なデモである

LangChain のブログは，ほかに Browserbase の Kyle Jeong によるブラウザ操作エージェント，Ryan Vogel による大規模なメールトリアージ，Jarrod Watts による取引エージェントを挙げている．ただし本調査では，それらを本番運用の実績として確認はできていない．

### 使いどころの整理

- 判定が高頻度で，LLM のコストと遅延が支配的になる箇所（全メッセージのガードレール，全リクエストのルーティング）
- 確率で自動実行と人間へのエスカレーションを切り替えたい箇所
- データを外部に出したくない場合は，Nimble，Kev，Laya などローカル実行可能なモデル

一方，説明文の生成，複雑な多段推論，画像入力は対象外である（Nimble は画像や説明文の生成ができないと明記している）．

## ポイント

- Decision Model は「生成せず，型付き判定と確率を返す」モデルの系統で，Jev が先行し，発表直後にオープンな追随が複数出た
- 実装は，LLM に LoRA を当てる系統（Nimble，Kev）と，小型エンコーダに決定ヘッドを付ける系統（Laya，Von）に大別できる
- 分類器，構造化出力，LLM-as-judge，ガードレールモデルとは，質問を実行時に与える点と確率を返す点で区別される
- 較正された確率が価値の核だが，数値はどれも各ベンダーの自己評価で，指標も揃っていない
- 事例は，公式ユースケースと個人リポジトリが中心で，企業の本番導入は確認できなかった
- 発表直後の動きの速い領域で，数値や版が頻繁に変わる

## 関連項目

- [[Jev]] - 先行する商用の System One Model
- [[Nimble]] - Qwen3.5-9B + LoRA のオープンな判定モデル
- [[ハーネスエンジニアリング]] - ルーティング・ガードレールなど判定部品の置き場所
- [[LoRA]] - Nimble や Kev が使う軽量ファインチューニング
- [[LLM]]
- [[RLHF]] - Jev が批判し，RLCD で置き換えを図る学習方式
- [[LLM-as-a-Judge]]
- [[ガードレール]]
- [[Kev]]
- [[Laya]]

## 参考

- [TypeSafe AI 公式サイト](https://typesafe.ai/)
- [TypeSafe AI Docs: Example use cases](https://docs.typesafe.ai/concepts/use-case-map)
- [What Is Jev? A Guide to TypeSafe AI's System One Model | LangChain Blog](https://www.langchain.com/blog/building-a-harness-with-jev)
- [Jev on Fly.io Sprites](https://fly.io/sprites/jev/)
- [TypeSafe AI Releases Jev | MarkTechPost](https://www.marktechpost.com/2026/09/19/typesafe-ai-releases-jev/)
- [GitHub - bespokelabsai/nimble](https://github.com/bespokelabsai/nimble)
- [bespokelabs/Bespoke-Nimble-9B | Hugging Face](https://huggingface.co/bespokelabs/Bespoke-Nimble-9B)
- [GitHub - jaredpalmer/kev](https://github.com/jaredpalmer/kev)
- [GitHub - NandhaKishorM/laya](https://github.com/NandhaKishorM/laya)
- [GitHub - wfzyx/von](https://github.com/wfzyx/von)
- [GitHub - az9713/jev-email-triage](https://github.com/az9713/jev-email-triage)
- [GitHub - jarrodwatts/jev-trader](https://github.com/jarrodwatts/jev-trader)
