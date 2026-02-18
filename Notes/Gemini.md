---
title: "Gemini"
date: 2026-02-14
tags:
  - AI
  - LLM
related:
  - "[[RAG]]"
  - "[[MCP]]"
  - "[[Claude Code]]"
  - "[[TPU]]"
---

## 概要

Gemini は Google DeepMind が開発したマルチモーダル大規模言語モデル（LLM）ファミリーである。テキスト、画像、音声、動画、コードを統一的に理解・生成できるネイティブマルチモーダル設計を特徴とし、2024年2月に Google Bard から Gemini へとブランド統合された。2025年11月に最新世代の Gemini 3 がリリースされ、月間アクティブユーザーは4.5億人に達している。

## 詳細

### 歴史と進化

| 時期 | モデル / 製品 | 概要 |
|------|-------------|------|
| **2021年** | LaMDA | Google AI の対話型 AI 基盤 |
| **2023年3月** | Bard（LaMDA 搭載） | ChatGPT 対抗として公開 |
| **2023年5月** | Bard（PaLM 2 搭載） | PaLM 2 にアップグレード、180カ国展開 |
| **2023年12月** | Gemini 1.0 | Ultra / Pro / Nano の3モデルを発表。初のネイティブマルチモーダル LLM |
| **2024年2月** | Bard → Gemini リブランド | Bard と Duet AI を Gemini ブランドに統合。Gemini Advanced 開始 |
| **2024年2月** | Gemini 1.5 | Mixture-of-Experts アーキテクチャ採用。100万トークンのコンテキストウィンドウ |
| **2024年12月** | Gemini 2.0 Flash | マルチモーダルライブ API、エージェント機能、Jules（コーディングエージェント）発表 |
| **2025年1月** | Gemini 2.0 Flash/Pro | Gemini 2.0 Flash がデフォルトモデルに |
| **2025年3月** | Gemini 2.5 Pro | 「思考モデル」として拡張推論能力を搭載 |
| **2025年6月** | Gemini CLI | オープンソースのターミナル AI エージェント |
| **2025年11月** | Gemini 3 Pro | 最新世代。Deep Think モード、エージェント推論の大幅強化 |

### モデルファミリー（2026年2月時点）

| モデル | 特徴 | コンテキスト | 入力価格（/1Mトークン） | 出力価格（/1Mトークン） |
|--------|------|-------------|------------------------|------------------------|
| **Gemini 3 Pro** | 最高性能。Deep Think、エージェント推論 | 100万トークン | $2.00 | $12.00 |
| **Gemini 3 Flash** | フロンティア性能＋高速 | 100万トークン | $0.50 | $3.00 |
| **Gemini 2.5 Pro** | コーディングに最適化 | 100万トークン | $1.25 | $10.00 |
| **Gemini 2.5 Flash** | コスパと推論のバランス | 100万トークン | 低コスト | 低コスト |
| **Gemini 2.5 Flash-Lite** | 最低コスト | — | $0.10 | $0.40 |

※ 200K トークン超のコンテキストでは価格が約2倍になる

### アーキテクチャの特徴

- **ネイティブマルチモーダル**: テキストコーパスだけでなく、画像・音声・動画・コードを含む多種多様なデータで事前学習。入力から出力まで統一的にマルチモーダル処理
- **Mixture-of-Experts（MoE）**: Gemini 1.5 以降で採用。入力に応じて最適な「エキスパート」サブネットワークを活性化し、効率と性能を両立
- **100万トークンコンテキスト**: 長大なドキュメント、コードベース全体、動画の処理が可能
- **Deep Think モード**: Gemini 3 で導入。複数の可能性を評価し、自身の論理を検証してから回答する深い推論能力

### Gemini 3 の主要機能

- **エージェント推論**: 複雑な目標を複数ステップの実行計画に分解し、自律的に実行。2025年後半には Computer Use（ブラウザ・ローカル環境の制御）にも対応
- **コード生成**: 2.5 Pro 比で50%以上のベンチマーク改善。自然言語からアプリケーションを生成する「Vibe Coding」にも対応
- **マルチモーダル理解**: MMMU-Pro（画像推論）、Video MMMU（動画理解）で最高スコア
- **ドキュメント理解**: OCR を超える複雑なドキュメントの構造理解と推論
- **低レイテンシ**: 最適化によりリアルタイムの対話応答を実現

### API と開発者ツール

**アクセス方法:**

| プラットフォーム | 対象 |
|-----------------|------|
| **Google AI Studio** | プロトタイピング、Vibe Coding |
| **Vertex AI** | エンタープライズ向けプロダクション環境 |
| **Gemini API** | 直接 API アクセス |
| **Gemini CLI** | ターミナルからの AI エージェント操作 |
| **Google Antigravity** | エージェント開発環境 |

**主要な API 機能:**

- **ストリーミングレスポンス**: リアルタイムでの応答出力
- **Function Calling**: 外部ツール・API との連携
- **Google Search Grounding**: 最新情報での応答の根拠付け
- **コンテキストキャッシュ**: 頻繁に使用するプロンプトをキャッシュし、コスト90%削減
- **バッチ処理**: 50%のコスト削減

**コスト最適化のポイント:**

- 用途に応じたモデル選択が最も効果的（軽量タスクには Flash-Lite、複雑な推論には 3 Pro）
- コンテキストキャッシュで繰り返し入力のコストを大幅削減
- バッチ処理で非リアルタイムタスクのコストを半減

### 他の主要 LLM との比較

| 観点 | Gemini | ChatGPT（GPT-5.2） | Claude |
|------|--------|---------------------|--------|
| **コンテキスト** | 100万トークン | 12.8万トークン | 20万〜100万トークン |
| **マルチモーダル** | ネイティブ（最も強力） | 対応 | 対応 |
| **コーディング** | 強い（SWE-bench 63.8%） | 最強（SWE-bench 80%） | 強い（SWE-bench 70.3%） |
| **推論品質** | 高い | 高い | 最高（複雑な論理） |
| **エコシステム統合** | Google Workspace | ChatGPT プラットフォーム | [[Claude Code]]、Cursor 等 |
| **入力価格** | $1.25/M（2.5 Pro） | $1.75/M（GPT-5.2） | プレミアム価格帯 |
| **市場シェア** | 約18% | 約68% | ニッチ（開発者向け） |

### ユースケース

- **コード生成・レビュー**: 自然言語からのアプリ生成、レガシーコードの近代化、マルチモーダルなアーキテクチャレビュー
- **ドキュメント処理**: 法務文書の分析、契約書レビュー、長文の要約（100万トークンコンテキストを活用）
- **カスタマーサポート**: マルチモーダル対応のチャットボット構築
- **コンテンツ生成**: マーケティングコンテンツ、ブログ記事の自動生成
- **画像・動画分析**: EC 商品画像の分類、動画からの情報抽出
- **[[RAG]] パイプライン**: 長大なコンテキストウィンドウを活かした検索拡張生成

### Google エコシステムとの統合

2025年以降、Gemini は Google Workspace に深く統合されている:

- **Gmail**: 複雑なメールスレッドの下書き、要約
- **Google Docs**: ドキュメントの自動生成、編集支援
- **Google Sheets**: データ可視化の自動生成
- **Google Meet**: 会議の要約、議事録生成
- **Google Search**: AI Overview による検索結果の要約

## ポイント

- Gemini は Google DeepMind が開発したネイティブマルチモーダル LLM で、テキスト・画像・音声・動画・コードを統一処理
- 2023年の Bard → 2024年の Gemini リブランド → 2025年の Gemini 3 と急速に進化
- 100万トークンのコンテキストウィンドウは業界最大級で、大規模ドキュメント処理に強み
- コスト面では Flash-Lite（$0.10/M）から 3 Pro（$2.00/M）まで幅広い価格帯をカバー
- Google Workspace との深い統合が差別化ポイント。開発者向けには Gemini API、Gemini CLI、Vertex AI を提供

## 関連項目

- [[RAG]] - Gemini の長大コンテキストを活かした検索拡張生成
- [[MCP]] - AI モデルと外部ツールの接続標準（Gemini も対応）
- [[Claude Code]] - 競合する AI コーディングツール
- [[TPU]] - Gemini モデルの学習・推論インフラとなる Google 製 AI 専用チップ

## 参考

- [Google DeepMind - Gemini](https://deepmind.google/models/gemini/)
- [Gemini API Documentation](https://ai.google.dev/gemini-api/docs/models)
- [Gemini Developer API Pricing](https://ai.google.dev/gemini-api/docs/pricing)
- [Gemini 3 for Developers - Google Blog](https://blog.google/technology/developers/gemini-3-developers/)
- [Google Gemini - Wikipedia](https://en.wikipedia.org/wiki/Google_Gemini)
- [Google Gemini Cookbook - GitHub](https://github.com/google-gemini/cookbook)
- [AI Model Benchmarks - LM Council](https://lmcouncil.ai/benchmarks)
- [Gemini 2.0 Deep Dive: Code Execution - Google Developers Blog](https://developers.googleblog.com/en/gemini-20-deep-dive-code-execution/)
