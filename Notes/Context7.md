---
title: "Context7"
date: 2026-02-19
tags:
  - Tool
  - AI
  - Agent
related:
  - "[[MCP]]"
  - "[[Claude Code]]"
  - "[[RAG]]"
---

## 概要

Context7 は Upstash が開発した [[MCP]] サーバー。AI コーディングアシスタントがライブラリ・フレームワークの最新ドキュメントをリアルタイムで取得し、プロンプトに注入する。LLM のトレーニングデータのカットオフ日以降に更新されたライブラリの API についてのハルシネーション（存在しない API の生成）を防ぐことを目的とする。[[MCP]] のノートで簡単に触れている。

## 詳細

### 解決する問題：LLM のドキュメントの陳腐化

LLM は学習データのカットオフ日時点の知識しか持たないため、以下のような問題が発生する：

- Next.js 15・React 19 など、カットオフ後に大きく変更されたライブラリで誤った API を生成
- 廃止されたメソッドや変更されたシグネチャを現役 API として提案
- 「〜という機能はどのライブラリにもない」と誤って判断

Context7 はプロンプトに現在の公式ドキュメントを自動注入することで、これらの問題を解消する。

### 仕組み

#### ドキュメントのインデックス化

Context7 は対象ライブラリのドキュメントを以下の手順で処理してインデックス化する：

1. **Parse（抽出）**: ドキュメントからコードスニペット・説明文を抽出
2. **Enrich（補強）**: LLM を使って各部分に短い説明とメタデータを付与
3. **Vectorize（ベクトル化）**: セマンティック検索のためにコンテンツを埋め込みベクトルに変換
4. **Rerank（再スコアリング）**: 独自アルゴリズムで関連性スコアを付与
5. **Cache（キャッシュ）**: Redis でキャッシュし、高速なレスポンスを実現

#### MCP ツールとして動作

Context7 は2つの [[MCP]] ツールを提供する：

| ツール | 役割 |
|-------|------|
| `resolve-library-id` | ライブラリ名（例: "Next.js"）を Context7 内部 ID に解決 |
| `query-docs` | ライブラリ ID と質問を受け取り、関連ドキュメント・コードスニペットを返す |

LLM がライブラリ情報を必要とするとき、これらのツールを自動的に呼び出してドキュメントを取得し、プロンプトに注入する。

### セットアップ

#### [[Claude Code]] への追加

```bash
claude mcp add context7 npx @upstash/context7-mcp@latest
```

#### claude_desktop_config.json での設定

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp@latest"]
    }
  }
}
```

#### 使い方

プロンプト内で自然に要求するだけでよい：

```
"Next.js 15 の App Router でのキャッシュ戦略を教えて。
use context7 で最新ドキュメントを参照して"
```

または Context7 MCP が自動的に呼び出されるよう設定すれば、「use context7」と明示せずとも動作する。

### 対応ライブラリ

Upstash が独自にインデックスしているライブラリのほか、ユーザーが新しいライブラリの追加をリクエストできる。主要なライブラリはほぼ網羅されている：

- Next.js・React・Vue・Angular・Svelte
- Node.js・Deno・Bun
- LangChain・LlamaIndex・Hugging Face
- Supabase・Firebase・Prisma
- Tailwind CSS・shadcn/ui など

### [[RAG]] との関係

Context7 は広義の [[RAG]]（Retrieval-Augmented Generation）の一実装と見なせる。ユーザーのプロンプトを受け取り、関連するドキュメントをベクトル検索でリトリーブし、LLM のコンテキストに注入する点で同じ構造を持つ。異なるのは：

- **特化対象**: ドキュメント・コードスニペットのみを対象とする（汎用的な知識ではなく）
- **インターフェース**: [[MCP]] プロトコルで AI ツールから透過的に呼び出せる
- **鮮度**: ライブラリのリリースに追随して定期的に更新される

## ポイント

- LLM のカットオフ日以降の最新ライブラリドキュメントをリアルタイムで注入し、ハルシネーションを防ぐ
- [[MCP]] サーバーとして動作し、`resolve-library-id` + `query-docs` の2ツールを提供
- Upstash の Redis でキャッシュされ高速レスポンス。独自アルゴリズムで関連性リランキング
- [[Claude Code]]・Cursor・Windsurf など主要 AI コーディングツールから利用可能
- 広義の [[RAG]] の一実装。ドキュメント特化・MCP 統合・高頻度更新が特徴

## 関連項目

- [[MCP]] - Context7 が実装するプロトコル。Claude Code 等から呼び出す
- [[Claude Code]] - Context7 を利用する主要な MCP クライアント
- [[RAG]] - Context7 が実装するアーキテクチャパターン

## 参考

- [GitHub - upstash/context7](https://github.com/upstash/context7)
- [Context7 MCP: Up-to-Date Docs for Any Cursor Prompt | Upstash Blog](https://upstash.com/blog/context7-mcp)
- [Introducing Context7: Up-to-Date Docs for LLMs and AI Code Editors | Upstash Blog](https://upstash.com/blog/context7-llmtxt-cursor)
- [Context7 MCP: Stop LLM Hallucinations with Live Docs](https://www.trevorlasn.com/blog/context7-mcp)
