---
title: "LMStudio"
date: 2026-02-24
tags:
  - AI
  - LLM
  - Tool
related:
  - "[[Ollama]]"
  - "[[RAG]]"
  - "[[MCP]]"
---

## 概要

LM Studio（LMStudio）はローカル環境で LLM を GUI から簡単に実行できる無料のデスクトップアプリケーション。Hugging Face からモデルをブラウズ・ダウンロードし、チャット UI・REST API・ドキュメント添付 RAG を即座に利用できる。エンジニア以外のユーザーも含めた「最も簡単なローカル LLM 環境」として広く知られる。

## 詳細

### 基本情報

| 項目 | 内容 |
|------|------|
| 開発元 | LM Studio, Inc. |
| ライセンス | フリーミアム（個人・研究利用は無料） |
| 対応 OS | macOS・Windows・Linux |
| 公式サイト | lmstudio.ai |
| バックエンド | llama.cpp ベース |

### 主要機能

#### モデルブラウザ

Hugging Face 上の GGUF 形式モデルをアプリ内から直接検索・ダウンロード・管理できる。モデルの速度・品質インジケータが視覚的に表示され、ハードウェアに合ったモデル選択を支援する。

対応モデル例：

| モデル | 特徴 |
|--------|------|
| Llama 3.x（Meta） | 汎用性が高い定番モデル |
| Qwen 2.5 | 日本語を含む多言語で高性能 |
| DeepSeek-R1 | 推論特化 |
| Gemma 3（Google） | 軽量で高品質 |
| OpenAI gpt-4o-mini-oss | OpenAI 初のオープンソース LLM |

#### チャット UI

- スレッド管理
- システムプロンプトの設定
- パラメータ（temperature・top_p など）のスライダー調整
- マルチモーダル（ビジョン対応モデルで画像添付可）

#### ドキュメント添付（ローカル RAG）

```
ドキュメント（PDF・TXT 等）をチャットにドラッグ&ドロップ
→ LM Studio がオフラインで内容を解析
→ ドキュメントに基づいた Q&A が可能
```

#### OpenAI 互換 REST API

```python
from openai import OpenAI

# LM Studio のローカルサーバーを向ける
client = OpenAI(
    base_url="http://localhost:1234/v1",
    api_key="lm-studio"  # ダミーキーで OK
)

response = client.chat.completions.create(
    model="lmstudio-community/Meta-Llama-3.1-8B-Instruct-GGUF",
    messages=[{"role": "user", "content": "こんにちは"}]
)
```

#### MCP サーバー統合

LM Studio に MCP（Model Context Protocol）サーバーをインストールし、ローカルモデルと組み合わせて使用できる（[[MCP]] 参照）。

### ハードウェア最適化

| 環境 | 最適化 |
|------|--------|
| Apple Silicon（M1/M2/M3/M4） | Apple Metal GPU・MLX フレームワーク |
| NVIDIA GPU | CUDA オフロード |
| AMD GPU | Vulkan オフロード |
| Intel GPU | Vulkan オフロード |
| CPU のみ | llama.cpp の CPU 推論 |

### [[Ollama]] との比較

| 観点 | LM Studio | Ollama |
|------|-----------|--------|
| インターフェース | GUI（直感的） | CLI（コマンドライン） |
| ターゲット | 非エンジニアも含む幅広いユーザー | エンジニア・開発者 |
| インストール | .dmg/.exe からインストール | brew/curl 1行 |
| モデル管理 | アプリ内ブラウザ | `ollama pull` コマンド |
| API | OpenAI 互換（ポート 1234） | OpenAI 互換（ポート 11434） |
| MCP 対応 | あり | あり |
| モデル形式 | GGUF（Hugging Face） | Ollama 独自形式（内部は GGUF） |
| カスタマイズ性 | 低い（GUI 操作に限定） | 高い（Modelfile でカスタム可） |

### ユースケース

- エンジニア以外のチームメンバーが LLM を試す環境の提供
- プライバシー重視の文書処理（社外秘ドキュメントを外部サービスに送りたくない）
- オフライン環境での LLM 活用
- API として自前アプリケーションに組み込む

## ポイント

- GUI での操作が中心のため、コマンドライン不要でローカル LLM を体験できる
- OpenAI 互換 API により、`base_url` を変えるだけで既存の OpenAI 実装が流用可能
- Apple MLX 対応により M シリーズ Mac では高速推論が可能
- 商用利用や組織デプロイには有料ライセンスが必要な場合がある（公式サイト確認推奨）

## 関連項目

- [[Ollama]] - CLI 中心のローカル LLM ツール（開発者向け）
- [[RAG]] - LM Studio のドキュメント添付機能でローカル RAG を実現
- [[MCP]] - LM Studio に MCP サーバーを統合してツール利用が可能

## 参考

- [LM Studio 公式サイト](https://lmstudio.ai/)
- [LM Studio ドキュメント](https://lmstudio.ai/docs/app)
- [LM Studio モデルカタログ](https://lmstudio.ai/models)
