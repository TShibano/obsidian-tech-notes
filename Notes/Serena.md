---
title: "Serena"
date: 2026-02-19
tags:
  - Tool
  - AI
  - Agent
related:
  - "[[MCP]]"
  - "[[Claude Code]]"
---

## 概要

Serena は Oraios AI が開発したオープンソースの [[MCP]] サーバー型 AI コーディングエージェントツールキット。LSP（Language Server Protocol）を基盤にコードベースをシンボルレベルで意味的に理解し、ファイル全体を渡すのではなく関数・クラス・変数などの構造を把握して、より正確なコード検索・編集を実現する。Cursor・Windsurf などの有料ツールの代替として、無料で高品質なコードベース理解を提供する。

## 詳細

### 誕生の背景と位置づけ

大規模コードベースを AI エージェントで操作する際、ファイルの全文をコンテキストに渡すアプローチには限界がある（コンテキスト消費・ノイズ増大・大規模プロジェクトでの非効率）。Serena はこの問題を LSP の力を借りて解決する。開発者が IDE で「定義に移動」「全参照を検索」などを使う感覚で、AI エージェントがコードを把握・編集できるようにする。

### コア技術：LSP（Language Server Protocol）

LSP は VS Code などのモダン IDE でコードの意味的な理解（型補完・参照解析・リファクタリング）を実現するプロトコル。Serena はこの LSP を MCP サーバーとして AI エージェントに公開することで、シンボルレベルの操作を可能にする。

| IDE の操作 | Serena での対応 |
|-----------|--------------|
| 定義に移動 | シンボルの定義を取得 |
| 全参照を検索 | シンボルを使用している箇所を列挙 |
| シンボルの名前変更 | プロジェクト全体での一括変更 |
| コードアウトライン | ファイル・モジュールの構造把握 |

### 主要機能

#### セマンティック検索・取得

- **シンボルレベルのナビゲーション**: 関数・クラス・変数を単位として検索
- **参照追跡**: あるシンボルがどこで定義・使用されているかをプロジェクト全体で追跡
- **構造的コード理解**: ファイルの中身を丸ごと読むのではなく、必要な構造だけを抽出

#### セマンティック編集

- **ファイルのアウトライン取得**: ファイル内のクラス・関数の一覧を取得
- **対象を絞った編集**: 関数シグネチャの変更・特定ブロックへの挿入など、精度の高い編集

#### 対応言語

LSP ライブラリにより 30 以上のプログラミング言語をサポート：

Python・TypeScript・JavaScript・Java・C#・Go・Rust・Kotlin・Swift・Ruby・PHP・C/C++・Bash・Dart・Scala・Haskell・Lua・Perl・R・Julia など

### セットアップ

#### インストール

```bash
# uvx（推奨）
uvx serena

# pip
pip install serena
```

#### [[Claude Code]] との連携

```bash
# MCP サーバーとして登録
claude mcp add serena uvx serena
```

#### claude_desktop_config.json での設定

```json
{
  "mcpServers": {
    "serena": {
      "command": "uvx",
      "args": ["serena"]
    }
  }
}
```

### ファイルベース vs Serena のアプローチ比較

| 観点 | ファイルベース | Serena（LSP ベース） |
|------|------------|------------------|
| **コード取得** | ファイル全文をコンテキストに渡す | 必要なシンボルだけを抽出 |
| **トークン消費** | 多い（無駄なコードも含む） | 少ない（構造的に絞り込み） |
| **大規模プロジェクト** | ファイル数が多いと困難 | プロジェクト全体を構造的に把握 |
| **参照追跡** | テキスト検索に依存 | セマンティックな参照解析 |
| **精度** | テキストベースで誤検知あり | シンボルレベルで高精度 |

### 現在の状況（2026年2月時点）

- **バージョン**: v0.1.4（v1.0.0 リリース準備中）
- **開発元**: Oraios AI
- **ライセンス**: オープンソース（無料）
- **対応クライアント**: [[Claude Code]]（Claude Desktop）・VS Code（GitHub Copilot）・ChatGPT（近日対応予定）

## ポイント

- LSP を活用してコードをシンボルレベルで意味的に理解する [[MCP]] サーバー
- ファイル全文を渡すのではなく、関数・クラス・変数単位で操作することでトークン消費を削減
- 30 以上の言語に対応。大規模コードベースの AI 操作に特に有効
- Cursor・Windsurf などの有料ツールの代替として無料で高品質なコードベース理解を提供
- [[Claude Code]] から `claude mcp add serena uvx serena` で簡単に追加可能

## 関連項目

- [[MCP]] - Serena が実装するプロトコル
- [[Claude Code]] - Serena を MCP サーバーとして利用する主要クライアント

## 参考

- [GitHub - oraios/serena](https://github.com/oraios/serena)
- [Serena MCP: Free AI Coding Agent with Full Codebase Understanding | SmartScope](https://smartscope.blog/en/generative-ai/claude/serena-mcp-coding-agent/)
- [How to Set Up Serena MCP Server | APIdog](https://apidog.com/blog/serena-mcp-server/)
- [Deconstructing Serena's MCP-Powered Semantic Code Understanding Architecture | Medium](https://medium.com/@souradip1000/deconstructing-serenas-mcp-powered-semantic-code-understanding-architecture-75802515d116)
