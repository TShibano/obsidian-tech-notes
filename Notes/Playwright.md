---
title: "Playwright"
date: 2026-02-19
tags:
  - Tool
  - Web
  - AI
related:
  - "[[MCP]]"
  - "[[Claude Code]]"
---

## 概要

Playwright は Microsoft が開発したオープンソースの Web ブラウザ自動化・E2E テストフレームワーク。Chromium・Firefox・WebKit の3大エンジンを単一 API で操作でき、JavaScript/TypeScript・Python・Java・C# など複数言語に対応する。2025年には AI エージェント機能（Test Agents）と [[MCP]] サーバーが追加され、AI 駆動のブラウザ自動化の中核ツールとなった。

## 詳細

### 基本概念と誕生の背景

Playwright は 2020年に Microsoft がリリース。先行ツールの Selenium・Puppeteer の課題（不安定なテスト・単一ブラウザ対応・遅い実行速度）を解決するために設計された。2025年現在、GitHub スター数 75,000 超・NPM 月間 2,000 万ダウンロード以上を記録し、最も急成長している E2E テストフレームワークと評される。

### 主要機能

#### クロスブラウザ・クロスプラットフォーム対応

| ブラウザエンジン | 対応ブラウザ |
|--------------|-----------|
| Chromium | Chrome・Edge |
| Firefox | Firefox |
| WebKit | Safari |

Windows・macOS・Linux のすべてで動作。ヘッドレス（CI 環境）・ヘッドあり（デバッグ）の両モードに対応。

#### 対応プログラミング言語

- JavaScript / TypeScript（Node.js）
- Python（`playwright-python`）
- Java
- C#（.NET）

#### 主要な自動化 API

```typescript
import { chromium } from '@playwright/test';

const browser = await chromium.launch();
const page = await browser.newPage();
await page.goto('https://example.com');
await page.click('button[data-testid="submit"]');
await expect(page.locator('h1')).toHaveText('Success');
await browser.close();
```

#### Auto-waiting（自動待機）

要素の出現・クリック可能状態・ネットワーク完了などを自動で待機するため、`sleep()` による明示的な待機が不要。テストの安定性が大幅に向上する。

#### トレース・デバッグ機能

- **Trace Viewer**: テスト実行の全ステップをタイムライン形式で記録・再生
- **スクリーンショット・動画**: 失敗時に自動保存
- **UI モード**: インタラクティブにテストを実行・デバッグ

### Playwright MCP サーバー

2025年3月、Microsoft が公式 Playwright MCP サーバーをリリース。LLM・AI エージェントがブラウザを操作するためのインターフェースを提供する。

#### 特徴

- **アクセシビリティツリーベース**: ピクセル入力や画像認識ではなく、ブラウザのアクセシビリティツリー（構造化テキスト）を通じてページを操作
- **ビジョンモデル不要**: テキストベースの構造化データで動作するため、軽量かつ高速
- **[[Claude Code]] との統合**: Claude Code から直接ブラウザを操作可能

#### インストール設定

```json
// claude_desktop_config.json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp@latest"]
    }
  }
}
```

#### ユースケース（[[MCP]] クライアントから）

```
"playwright mcp を使って example.com を開いて、
ログインフォームに入力して送信してください"
```

### Playwright Test Agents（AI エージェント機能）

Playwright v1.56（2025年）で追加された AI エージェント機能。LLM を活用してテスト設計からコード生成・自動修復までを自動化する。

#### 3つのエージェント

| エージェント | 役割 |
|-----------|------|
| **Planner** | Web アプリのテスト計画を Markdown 形式で自動生成 |
| **Generator** | テスト計画をもとに Playwright テストコードを自動生成 |
| **Healer** | テスト失敗時にコードの問題を自動修復 |

#### セットアップ

```bash
npx playwright init-agents --loop=vscode
```

実行すると `.github/chatmodes/` 配下に3つのエージェント定義ファイルと `tests/seed.spec.ts` が生成される。

#### 対応 AI ツール

- VS Code + GitHub Copilot
- [[Claude Code]]
- OpenCode

### Microsoft Playwright Testing（クラウドサービス）

Azure 上で Playwright テストを並列実行するマネージドサービス。CI/CD パイプラインとの統合でテスト実行時間を大幅に短縮できる。

## ポイント

- Chromium・Firefox・WebKit を単一 API で操作する。Auto-waiting でテストが安定
- 2025年に Playwright MCP サーバーが登場。AI エージェントがブラウザを構造的に操作可能に
- Test Agents により、テスト設計・コード生成・自動修復を AI が担うフローが実現
- [[MCP]] 経由で [[Claude Code]] や Cursor などの AI ツールからブラウザ自動化が可能
- GitHub スター 75,000 超で急成長。Selenium・Cypress に次ぐ主要 E2E テストフレームワーク

## 関連項目

- [[MCP]] - Playwright MCP サーバーを通じて AI エージェントとブラウザを連携
- [[Claude Code]] - Playwright MCP を利用してブラウザ操作・テスト自動化に活用

## 参考

- [Playwright 公式サイト](https://playwright.dev/)
- [GitHub - microsoft/playwright](https://github.com/microsoft/playwright)
- [GitHub - microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp)
- [テスト自動化フレームワーク「Playwright」にAIエージェント機能 | Publickey](https://www.publickey1.jp/blog/25/playwrightai.html)
- [Playwright MCP を使用してブラウザ操作を自動化 | Microsoft Learn](https://learn.microsoft.com/ja-jp/microsoft-edge/playwright/)
- [Microsoft Playwright Testing | Azure](https://azure.microsoft.com/ja-jp/products/playwright-testing)
