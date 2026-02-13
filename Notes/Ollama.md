---
title: "Ollama"
date: 2026-02-13
tags:
  - AI
  - LLM
  - Tool
related:
  - "[[RAG]]"
  - "[[MLOps]]"
---

## 概要

OllamaはオープンソースのローカルLLM推論ツール。コマンド1行でLlama・DeepSeek・Qwen・Gemmaなどのオープンウェイトモデルをローカル環境で実行でき、OpenAI互換のREST APIも提供する。バックエンドは高速なC++推論ライブラリ `llama.cpp` を採用し、CPU・GPU（CUDA/Metal）の両方に対応。

## 詳細

### インストールと基本操作

```bash
# インストール（macOS）
brew install ollama

# サーバー起動
ollama serve

# モデルの取得・実行
ollama run llama3.2
ollama run deepseek-r1:7b
ollama run qwen2.5:14b

# ローカルに保存されたモデル一覧
ollama list

# 実行中モデル確認
ollama ps
```

### REST API

Ollamaはデフォルトで `http://localhost:11434` にAPIサーバーを立ち上げる。

| エンドポイント | メソッド | 説明 |
|----------------|---------|------|
| `/api/generate` | POST | テキスト補完（単発） |
| `/api/chat` | POST | チャット形式の推論 |
| `/api/tags` | GET | ローカルモデル一覧 |
| `/api/ps` | GET | 実行中モデル一覧 |
| `/api/pull` | POST | モデルのダウンロード |

```python
# Python での使用例（openai ライブラリで互換動作）
from openai import OpenAI

client = OpenAI(base_url="http://localhost:11434/v1", api_key="ollama")
response = client.chat.completions.create(
    model="llama3.2",
    messages=[{"role": "user", "content": "こんにちは"}]
)
```

### 対応モデル（2026年2月時点）

| モデル | 特徴 |
|--------|------|
| Llama 3.2（1B/3B）| Meta製、軽量で高速 |
| Llama 3.1（8B/70B/405B）| 汎用性が高い |
| DeepSeek-R1 | 推論特化、低リソースで強力 |
| Qwen 2.5（0.5B〜72B）| 多言語対応、日本語も優秀 |
| Gemma 3 | Google製、軽量モデル |
| Mistral | 欧州発、バランス良好 |

公式ライブラリ: https://ollama.com/library

### RAGとの組み合わせ

OllamaはLlamaIndex・LangChainと組み合わせることでローカル完結型の[[RAG]]システムを構築できる：

```python
# LlamaIndex + Ollama でのローカルRAG
from llama_index.llms.ollama import Ollama
from llama_index.embeddings.ollama import OllamaEmbedding

llm = Ollama(model="llama3.2", request_timeout=120.0)
embed_model = OllamaEmbedding(model_name="nomic-embed-text")
```

### 他ツールとの比較

| ツール | 特徴 | 向き不向き |
|--------|------|-----------|
| **Ollama** | 簡単・CLI重視・llama.cpp採用 | 個人・プロトタイプ・オフライン |
| **vLLM** | 高スループット・並列処理 | 本番API・大量リクエスト |
| **LM Studio** | GUI付き・初心者向け | 非エンジニアの試用 |
| **LocalAI** | Docker対応・高互換性 | サーバーデプロイ |

## ポイント

- `ollama run <モデル名>` の1コマンドでモデルのDL〜実行まで完結、ハードルが極めて低い
- OpenAI互換APIにより、既存のOpenAI SDK利用コードをほぼそのまま流用可能（`base_url` を変えるだけ）
- プライバシー重視・オフライン環境・コスト削減が必要な場面での用途に強い
- 単一ユーザー向けに設計されており、高並列リクエストには不向き（その場合はvLLMを検討）
- NVIDIA CUDA・Apple Metal・CPU の三者に対応し、M1/M2/M3 Mac でも十分な速度で動作

## 関連項目

- [[RAG]]
- [[MLOps]]

## 参考

- [Ollama 公式サイト](https://ollama.com)
- [GitHub - ollama/ollama](https://github.com/ollama/ollama)
- [Ollama API Documentation](https://github.com/ollama/ollama/blob/main/docs/api.md)
- [Ollama or vLLM? How to choose - Red Hat Developer](https://developers.redhat.com/articles/2025/07/08/ollama-or-vllm-how-choose-right-llm-serving-tool-your-use-case)
