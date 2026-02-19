---
title: "Claude Code"
date: 2026-02-05
tags:
  - Tool
  - CLI
  - AI
  - Agent
related:
  - "[[MCP]]"
  - "[[LLM]]"
  - "[[Entire CLI]]"
  - "[[Gemini]]"
  - "[[Playwright]]"
  - "[[Serena]]"
  - "[[Context7]]"
---

## 概要

Claude Code は Anthropic が開発したエージェント型コーディングツール。ターミナル上で動作し、コードベースを理解しながら自然言語コマンドでルーチンタスクの実行、複雑なコードの説明、Git ワークフローの管理などを支援する。

## 詳細

### 動作環境

Claude Code は複数のプラットフォームで利用可能:

- **ターミナル (CLI)**: コアとなる体験。任意のターミナルで `claude` コマンドを実行
- **IDE 拡張**: VS Code、Cursor、Windsurf、JetBrains で視覚的な差分表示
- **Web**: claude.ai/code からブラウザで利用（ローカルセットアップ不要）
- **GitHub 連携**: PR で @claude をメンションして利用

### エージェントとサブエージェント

Claude Code は単一の AI ではなく、複数の AI エージェントをオーケストレーションするシステム。Task ツールを使って最大7つのサブエージェントを並列実行できる。

#### 組み込みサブエージェント

| サブエージェント | 用途 |
|-----------------|------|
| **Explore** | コードベースの検索・分析に特化した高速な読み取り専用エージェント |
| **Plan** | プランモード時のコードベース調査を担当 |
| **general-purpose** | 探索と変更の両方が必要な複雑なマルチステップタスク向け |

#### カスタムサブエージェント

`.claude/agents/` にサブエージェントを定義することで、独自のタスクに特化したエージェントを作成できる。各サブエージェントは独立したコンテキストウィンドウを持ち、専用のツール権限を設定可能。

### スキル (Skills)

スキルは Claude Code の拡張機能で、再利用可能なワークフローを定義できる。

#### スキルの構造

```
.claude/skills/{skill-name}/
├── SKILL.md          # 必須: スキル定義
├── templates/        # オプション: テンプレートファイル
└── scripts/          # オプション: 実行スクリプト
```

#### SKILL.md の構成

```yaml
---
name: skill-name
description: スキルの説明
user-invocable: true
---

# スキルの指示（マークダウン）
```

- `name`: `/skill-name` としてスラッシュコマンドになる
- `description`: Claude が自動呼び出しを判断する際に使用
- `user-invocable: true`: ユーザーがスラッシュコマンドで呼び出し可能
- `disable-model-invocation: true`: Claude の自動呼び出しを禁止（デプロイなど副作用のある操作向け）

スキルは Claude Code だけでなく、Claude.ai や Claude Desktop でも利用可能。

### フック (Hooks)

フックは特定のイベントで自動実行されるシェルコマンド。

#### 主要なフックイベント

| イベント | タイミング | 用途例 |
|---------|----------|--------|
| `PreToolUse` | ツール実行前 | 入力の検証・変更、危険な操作のブロック |
| `PostToolUse` | ツール実行後 | コードフォーマット、テスト実行 |
| `Stop` | レスポンス完了時 | 通知、クリーンアップ |
| `SessionStart` | セッション開始時 | 環境セットアップ |
| `UserPromptSubmit` | ユーザー入力送信時 | 入力の前処理 |

#### PreToolUse の入力変更機能 (v2.0.10+)

PreToolUse フックはツール入力を実行前に変更可能。これにより:
- 透過的なサンドボックス化
- セキュリティ強制（dry-run フラグ、シークレット秘匿）
- チーム規約の適用（コミットメッセージ形式など）

#### 設定ファイル

```json
// ~/.claude/settings.json または .claude/settings.json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "command": "prettier --write $CLAUDE_FILE_PATH"
      }
    ]
  }
}
```

### MCP (Model Context Protocol)

MCP は AI ツール連携のオープンスタンダード。Claude Code は MCP サーバーを通じて外部ツールやデータソースに接続できる。

#### 主な機能

- **ツール連携**: Google Drive、Slack、GitHub、Postgres など
- **Tool Search**: ツール定義をオンデマンドで読み込み、コンテキストウィンドウを46.9%削減
- **リソース**: `@` メンションで MCP リソースを参照可能
- **MCP Apps**: ツールがリッチな UI を返却可能（2026年1月〜）

### 効果的な使い方

#### CLAUDE.md の活用

プロジェクトルートに `CLAUDE.md` を配置すると、Claude が毎回の会話開始時に読み込む。

```markdown
# プロジェクト名

## コーディング規約
- TypeScript strict mode を使用
- Prettier でフォーマット

## よく使うコマンド
- `npm run test`: テスト実行
- `npm run build`: ビルド
```

`/init` コマンドでプロジェクト構造に基づいた CLAUDE.md を自動生成できる。

#### コンテキスト管理

- **スコープを絞る**: 1つのチャットは1つの機能・プロジェクトに限定
- **`/clear` を頻繁に使う**: 新しいタスク開始時にコンテキストをクリア
- **コンパクションを避ける**: コンテキストが尽きると自動要約され、重要な情報が失われる可能性

#### 並列作業

Claude Code チームが推奨する最大の生産性向上策:
- 複数のワークスペースで複数の Claude セッションを並列実行
- 待ち時間を最小化し、コンテキストスイッチを減らす

#### プランモード

複雑な変更には必ずプランモードを使用:
- `Shift+Tab` でプランモードと通常モードを切り替え
- 実装前にアーキテクチャを検討

#### CLI ツールの活用

外部サービスとの連携には CLI ツールが最も効率的:
- GitHub: `gh` コマンド
- AWS: `aws` コマンド
- GCP: `gcloud` コマンド

### キーボードショートカット

| 操作 | ショートカット |
|-----|--------------|
| 停止 | `Escape`（Ctrl+C はターミナル終了） |
| 前のメッセージに移動 | `Escape` を2回 |
| 画像貼り付け | `Ctrl+V`（Cmd+V ではない） |
| プランモード切替 | `Shift+Tab` |

## ポイント

- Claude Code は単なるチャットボットではなく、複数のエージェントをオーケストレーションするシステム
- スキルでワークフローを再利用可能なコマンドとして定義できる
- フックで自動化（フォーマット、テスト、通知など）を実現
- MCP で外部ツール・データソースと連携
- CLAUDE.md でプロジェクト固有のコンテキストを永続化
- 並列作業とプランモードが生産性向上の鍵

## 関連項目

- [[MCP]]
- [[LLM]]
- [[GitHub Actions]]
- [[VS Code]]
- [[Entire CLI]]
- [[Gemini]] - 競合する Google 製 AI モデル
- [[Playwright]] - Playwright MCP を通じてブラウザ自動化・E2E テストに活用
- [[Serena]] - LSP ベースのコードベース理解 MCP サーバー。大規模プロジェクトでの AI コーディングに有効
- [[Context7]] - ライブラリの最新ドキュメントを注入する MCP サーバー

## 参考

- [Claude Code overview - 公式ドキュメント](https://code.claude.com/docs/en/overview)
- [Claude Code - GitHub](https://github.com/anthropics/claude-code)
- [Extend Claude with skills - 公式ドキュメント](https://code.claude.com/docs/en/skills)
- [Create custom subagents - 公式ドキュメント](https://code.claude.com/docs/en/sub-agents)
- [Automate workflows with hooks - 公式ドキュメント](https://code.claude.com/docs/en/hooks-guide)
- [Connect Claude Code to tools via MCP - 公式ドキュメント](https://code.claude.com/docs/en/mcp)
- [Best Practices for Claude Code - 公式ドキュメント](https://code.claude.com/docs/en/best-practices)
- [Introducing the Model Context Protocol - Anthropic](https://www.anthropic.com/news/model-context-protocol)
