---
title: "MCP"
date: 2026-02-13
tags:
  - AI
  - Agent
  - Tool
related:
  - "[[Claude Code]]"
  - "[[Entire CLI]]"
  - "[[RAG]]"
  - "[[Gemini]]"
  - "[[Playwright]]"
  - "[[Serena]]"
  - "[[Context7]]"
---

## 概要

MCP（Model Context Protocol）は、Anthropicが2024年11月に発表したオープン標準プロトコル。AIモデル（LLM）と外部ツール・データソース間の接続方法を標準化し、AIエージェントがファイルシステム・データベース・Web API等を統一的に利用できるようにする。2025年にOpenAI・Google・Microsoftが採用し、業界標準となった。

## 詳細

### 背景と目的

AIエージェントが外部リソースと連携する際、各サービスごとに個別のインテグレーションを実装するのはコストが高い。MCPはこの問題を解決するため、AI←→ツール間の通信を標準化したプロトコルで、Language Server Protocol（LSP）の設計思想を参考にJSON-RPC 2.0上で動作する。

### アーキテクチャ

```
MCP Client（AIアプリ）  ←→  MCP Server（ツール提供）
    （Claude Code等）              （DB, API等）
```

**3つのコアプリミティブ:**

| プリミティブ        | 役割                               |
| ------------- | -------------------------------- |
| **Tools**     | LLMが実行できる関数（データ取得・計算・外部API呼び出し等） |
| **Resources** | サーバーが公開するデータ（ファイル・DBレコード等）       |
| **Prompts**   | 再利用可能なプロンプトテンプレート                |

### 公式MCPサーバー（プリビルト）

Anthropicが提供する公式サーバー:
- Google Drive, Slack, GitHub, Git, PostgreSQL, Puppeteer, Filesystem 等

### 有名なMCPサーバ
#### GitHub
- GitHubにおけるリポジトリ管理やPR，Issueなどを操作できる
- https://github.com/github/github-mcp-server/blob/main/docs/installation-guides/install-claude.md

#### Context7
- ライブラリの最新のドキュメントを参照してくれるMCPサーバー
- https://github.com/upstash/context7#installation


### 自作方法

**Python（推奨: FastMCPを使用）:**

```python
# インストール
# uv add "mcp[cli]" httpx

from mcp.server.fastmcp import FastMCP

mcp = FastMCP("My Server")

@mcp.tool()
def calculate(a: int, b: int) -> int:
    """2つの整数を足し算する"""
    return a + b

@mcp.resource("data://config")
def get_config() -> str:
    """設定情報を返す"""
    return "config_value"

if __name__ == "__main__":
    mcp.run()
```

**TypeScript:**

```typescript
import { McpServer, StdioServerTransport } from "@modelcontextprotocol/sdk/server";
import { z } from "zod";

const server = new McpServer({ name: "my-server", version: "1.0.0" });

server.tool(
  "calculate",
  { a: z.number(), b: z.number() },
  async ({ a, b }) => ({
    content: [{ type: "text", text: String(a + b) }]
  })
);

const transport = new StdioServerTransport();
await server.connect(transport);
```

**公式SDK:**
- Python: https://github.com/modelcontextprotocol/python-sdk
- TypeScript: https://github.com/modelcontextprotocol/typescript-sdk

### Claude Codeでの利用

[[Claude Code]]はMCPを通じて外部ツールと連携できる。`claude mcp add <server-name> <command>` でサーバーを追加する。Tool Search機能によりコンテキストウィンドウを節約しながらツール定義をオンデマンドで読み込める。
インストール時に`--scope`引数でスコープの設定ができる
- `local`: 自分の身がClaude Codeを立ち上げたディレクトリ配下でのみ使う
- `project`: プロジェクト単位で保存する．
- `user`: ユーザディレクトリに保存する

### 普及状況

- **2024年11月**: Anthropicが発表
- **2025年3月**: OpenAIが公式採用
- **2025年後半**: Google、Microsoft等が採用、業界標準化
- **2025年12月**: Linux Foundation（AAIF）への移管。コミュニティサーバー数が16,000以上に

## ポイント

- MCPは「AIのためのUSB規格」と例えられる。一度実装すれば任意のMCP対応AIクライアントから利用可能
- トランスポートはStdio（ローカル）またはSSE/HTTP（リモート）を選択できる
- Anthropicが単独で管理していたが、2025年12月にLinux Foundation（AAIF）に移管し、OpenAI・Blockも共同運営に
- [[Claude Code]]、Cursor、Windsurf等の主要AIコーディングツールはMCPクライアントとして動作する

## 関連項目

- [[Claude Code]]
- [[Entire CLI]]
- [[RAG]]
- [[Gemini]] - MCP 対応の Google 製マルチモーダル LLM
- [[Playwright]] - Microsoft 製 E2E テストフレームワーク。Playwright MCP サーバーで AI エージェントがブラウザを操作可能
- [[Serena]] - LSP ベースのセマンティックコード理解 MCP サーバー。大規模コードベースの AI 操作に有効
- [[Context7]] - LLM 向けライブラリドキュメント提供 MCP サーバー。ハルシネーション防止に活用

## 参考

- [Introducing the Model Context Protocol - Anthropic](https://www.anthropic.com/news/model-context-protocol)
- [Build an MCP server - 公式ドキュメント](https://modelcontextprotocol.io/docs/develop/build-server)
- [GitHub - modelcontextprotocol/python-sdk](https://github.com/modelcontextprotocol/python-sdk)
- [GitHub - modelcontextprotocol/typescript-sdk](https://github.com/modelcontextprotocol/typescript-sdk)
- [Model Context Protocol Specification](https://modelcontextprotocol.io/specification/2025-11-25)
