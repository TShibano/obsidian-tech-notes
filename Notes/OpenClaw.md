---
title: "OpenClaw"
date: 2026-04-10
tags:
  - AI
  - Agent
  - Tool
related:
  - "[[Claude Code]]"
  - "[[Context7]]"
---

## 概要

OpenClaw は、OSS のパーソナル AI エージェントで、LLM（Claude, GPT, Gemini, Ollama 等）をメッセージングプラットフォーム（WhatsApp, Telegram, Slack, Discord 等）経由で操作できる。2025年11月に「Clawdbot」として公開され、2026年1月末に GitHub スター 10 万を1週間で達成。2026年3月時点で 24.7 万スター。急成長の一方、深刻な権限昇格脆弱性が相次いで報告されている。

## 詳細

### 経緯

- **2025年11月**: オーストリアの開発者 Peter Steinberger が「Clawdbot」として公開
- **2026年1月27日**: Anthropic の商標苦情を受け「Moltbot」にリネーム
- **2026年1月30日**: 「OpenClaw」に再リネーム
- **2026年2月14日**: Steinberger が OpenAI 入社を発表。非営利財団によるプロジェクト管理へ移行予定
- **2026年3月**: 中国政府が政府機関・国有企業での OpenClaw 使用を制限

### アーキテクチャ

「Brains & Muscles」アーキテクチャを採用:

- **Brains（頭脳）**: フロンティアモデル（Claude, GPT-4o 等）が複雑な推論・計画・ツール選択を担当
- **Muscles（筋肉）**: プラグイン・スキル経由で実際のアクション（ファイル操作、Web ブラウジング、シェルコマンド等）を実行
- **Gateway**: すべてのセッション・ルーティング・チャネル接続の「Single Source of Truth」として機能する単一プロセス

### 主要機能

- **マルチチャネル統合**: 1つの AI エージェントを WhatsApp, Telegram, Slack, Discord 等に同時デプロイ
- **永続メモリ**: セッション・プラットフォーム横断でコンテキストを保持し、ユーザーの好みを学習
- **MCP（Model Context Protocol）対応**: `mcporter` 統合により、GitHub, Postgres, Notion, ブラウザ自動化等の MCP サーバーとシームレス接続。ツールの発見・設定・呼び出しを統一インターフェースで管理
- **拡張可能なスキル**: ClawHub のコミュニティプラグインまたはカスタム Python/Node.js スキルで機能拡張
- **モデル非依存**: API キーを持ち込む形式で、Claude, GPT, Gemini, ローカル Ollama モデル等を自由に選択

### セキュリティ脆弱性

2026年2月〜4月に 137 件のセキュリティアドバイザリが報告され、深刻な権限昇格脆弱性が相次いだ:

#### CVE-2026-32922（CVSS 9.9）

- `device.token.rotate` 関数が新規発行トークンのスコープを呼び出し元のスコープに制限しない欠陥
- 低権限トークンから `operator.admin` 権限を取得可能
- 修正版: v2026.3.11（2026年3月13日）

#### CVE-2026-33579（Critical）

- `/pair approve` コマンドパスがセキュリティスコープを認可チェックに転送しない欠陥
- `operator.pairing` 権限のみのトークンで `operator.admin` を取得し、全接続ノードで任意コマンド実行が可能
- 修正版: v2026.3.28

#### 露出規模

- 公開されている 135,000 以上の OpenClaw インスタンスの **63%** が認証レイヤーなしで稼働
- 認証なしのインスタンスではネットワーク接続のみでペアリングアクセス→管理者権限取得が可能

## ポイント

- メッセージングプラットフォームを UI としたマルチチャネル AI エージェントの代表的 OSS
- 短期間で 24.7 万スターを獲得した爆発的成長。AI エージェントへの需要の高さを示す
- MCP 対応により外部ツール統合が容易だが、セキュリティ設計の甘さが急成長の裏面として露呈
- CVSS 9.9 の脆弱性を含む 137 件のアドバイザリは、AI エージェントのセキュリティ課題を象徴
- 認証なしインスタンスの 63% という数値は、セルフホスト型 AI ツール全般への警鐘
- 創設者の OpenAI 移籍と財団化により、ガバナンス体制の変化にも注意が必要

## 関連項目

- [[Claude Code]] - Anthropic 公式の CLI 型 AI コーディングツール
- [[Context7]] - MCP サーバーの一つ（MCP エコシステム関連）

## 参考

- [openclaw/openclaw - GitHub](https://github.com/openclaw/openclaw)
- [OpenClaw - Wikipedia](https://en.wikipedia.org/wiki/OpenClaw)
- [What is OpenClaw? - DigitalOcean](https://www.digitalocean.com/resources/articles/what-is-openclaw)
- [CVE-2026-32922: Critical Privilege Escalation in OpenClaw - ARMO](https://www.armosec.io/blog/cve-2026-32922-openclaw-privilege-escalation-cloud-security/)
- [CVE-2026-33579: OpenClaw Privilege Escalation - DEV Community](https://dev.to/olgabyte/openclaw-cve-2026-33579-unauthorized-privilege-escalation-via-pair-approve-command-fixed-l48)
- [How to Build and Secure a Personal AI Agent with OpenClaw - freeCodeCamp](https://www.freecodecamp.org/news/how-to-build-and-secure-a-personal-ai-agent-with-openclaw/)
