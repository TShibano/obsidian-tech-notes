---
title: "RAG"
date: 2026-02-13
tags:
  - AI
  - LLM
related:
  - "[[MLOps]]"
  - "[[Ollama]]"
  - "[[MCP]]"
  - "[[Gemini]]"
  - "[[エージェントサーチ]]"
---

## 概要

RAG（Retrieval-Augmented Generation）は、LLMの回答生成時に外部知識ベースからリアルタイムで関連情報を検索・取得して文脈に付加する手法。LLMの学習データ後の情報にも対応でき、幻覚（ハルシネーション）の抑制や企業固有の知識への回答を実現する。

## 詳細

### アーキテクチャ

基本的な RAG パイプラインは以下の3ステップで構成される：

1. **インデックス構築（Indexing）**: ドキュメントをチャンクに分割し、埋め込みモデルでベクトル化してベクトルDBに格納
2. **検索（Retrieval）**: クエリをベクトル化し、類似度の高いチャンクをベクトルDBから取得
3. **生成（Generation）**: 取得したチャンクをプロンプトに付加してLLMに渡し、回答を生成

### 主な進化形

| 手法 | 概要 |
|------|------|
| **Naive RAG** | 基本的なretriever-generatorパイプライン |
| **Hybrid RAG** | セマンティック検索＋BM25等の語彙検索を組み合わせ、精度向上 |
| **GraphRAG** | ナレッジグラフと組み合わせ、エンティティ間の関係を考慮した検索 |
| **Long RAG** | 短いチャンクではなく長い検索単位（段落・ドキュメント全体）を取得 |
| **Self-RAG** | LLM自身が検索の要否を判断し、生成内容を自己評価する |

### 自作方法

**フレームワークを使う方法（推奨）:**

```python
# LlamaIndex を使った基本的な RAG
from llama_index.core import VectorStoreIndex, SimpleDirectoryReader

documents = SimpleDirectoryReader("data/").load_data()
index = VectorStoreIndex.from_documents(documents)
query_engine = index.as_query_engine()
response = query_engine.query("質問文")
```

```python
# LangChain を使った RAG エージェント
from langchain_community.vectorstores import FAISS
from langchain_openai import OpenAIEmbeddings, ChatOpenAI
from langchain.chains import RetrievalQA

embeddings = OpenAIEmbeddings()
vectorstore = FAISS.from_documents(docs, embeddings)
retriever = vectorstore.as_retriever()
qa_chain = RetrievalQA.from_chain_type(llm=ChatOpenAI(), retriever=retriever)
```

**主要コンポーネントの選択肢:**

| コンポーネント | 選択肢 |
|----------------|--------|
| フレームワーク | LangChain, LlamaIndex |
| 埋め込みモデル | OpenAI text-embedding-3, Cohere, BGE, Ollama ローカルモデル |
| ベクトルDB | FAISS（軽量・ローカル）, Chroma, Qdrant, Weaviate, pgvector |
| LLM | OpenAI GPT, Anthropic Claude, Ollama（ローカル）|

### ベクトルDBの選択基準

- **FAISS**: 小〜中規模、ローカル実行、最速の近似最近傍探索
- **Chroma**: ローカル開発・プロトタイプ向け、Python-nativeで簡単セットアップ
- **Qdrant**: 本番環境向け、フィルタリング機能が豊富、Rust製で高速
- **Weaviate**: マルチモーダル対応、GraphQL API、クラウドホスティングあり
- **pgvector**: 既存のPostgreSQLに追加、SQL併用できるハイブリッド検索に強い

## ポイント

- RAGはLLMの知識の鮮度問題（学習データのカットオフ）とハルシネーションを同時に解決するアーキテクチャ
- チャンクサイズとオーバーラップ設計が検索精度に大きく影響する（一般的には512〜1024トークン）
- 2026年現在、Hybrid RAG（セマンティック＋BM25）が単一手法より高精度なためスタンダード化しつつある
- GraphRAGはエンタープライズ向けで注目度が高いが、ナレッジグラフ構築コストが高い
- 評価には RAGAS などのフレームワークを使い、Faithfulness・Relevance・Context Recall を計測する

## 関連項目

- [[MLOps]]
- [[Ollama]]
- [[MCP]]
- [[Large Tabular Model]]
- [[Gemini]] - 100万トークンコンテキストを活かした RAG パイプライン構築
- [[エージェントサーチ]] - エージェントが動的に検索戦略を選択する Agentic RAG / Agentic Retrieval

## 参考

- [What is RAG? - AWS](https://aws.amazon.com/what-is/retrieval-augmented-generation/)
- [Introduction to RAG | LlamaIndex Documentation](https://developers.llamaindex.ai/python/framework/understanding/rag/)
- [Build a RAG agent with LangChain](https://docs.langchain.com/oss/python/langchain/rag)
- [The Ultimate RAG Blueprint (2025/2026) - LangWatch](https://langwatch.ai/blog/the-ultimate-rag-blueprint-everything-you-need-to-know-about-rag-in-2025-2026)
