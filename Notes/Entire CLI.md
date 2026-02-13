---
title: "Entire CLI"
date: 2026-02-13
tags:
  - AI
  - Agent
  - Tool
  - CLI
related:
  - "[[Claude Code]]"
---

## 概要

Entire CLIは、元GitHub CEOのThomas Dohmke氏が創業したスタートアップ「Entire」が開発するオープンソースのAI開発支援ツール。AIエージェントが生成したコードの背景にあるプロンプト・会話記録・ツール呼び出しをGitブランチに記録し、AIコーディング時の透明性と監査可能性を確保する。

## 詳細

### 背景

AIコーディングエージェント（[[Claude Code]]、Gemini CLI、Cursor等）の普及により、コードベースの大部分がAIによって生成されるようになっている。この流れでは「なぜこのコードが生成されたか」という文脈が失われがちで、コードレビューや障害調査が困難になる問題が顕在化している。Entire CLIはこの課題を解決するために開発された。

### 主要プロダクト：Checkpoints

最初のプロダクトは「Checkpoints」という名称のCLIツール。以下の情報を「Checkpoint」単位で記録し、専用のGitブランチに保存する：

- AIとの会話トランスクリプト（プロンプト・レスポンス）
- 変更されたファイル
- ツール呼び出しの履歴
- トークン使用量

コードベース本体のブランチには影響を与えず、メタデータのみを別ブランチに管理する設計。

### 対応エージェント

- **現在**: Anthropic Claude Code、Google Gemini CLI
- **予定**: Codex、Cursor CLI、その他主要エージェント

### 会社情報

| 項目 | 内容 |
|------|------|
| 創業者 | Thomas Dohmke（元GitHub CEO、在任2021〜2025年8月） |
| 設立 | 2025年 |
| シード調達 | $6,000万（評価額$3億）|
| 主要投資家 | Felicis, M12 (Microsoft), Madrona |
| GitHub | https://github.com/entireio/cli |

### 将来の展望

Entire CLIは第一弾に過ぎず、将来的には以下の3層構造のプラットフォームを構築予定：

1. **Git互換データベース**: AIエージェントの出力を保存
2. **セマンティック推論レイヤー**: コードの意味的理解
3. **AIネイティブUI**: GitHubやGitLabと連携するエージェント管理インターフェース

## ポイント

- AIコーディング時代の「なぜこのコードが書かれたか」という文脈を保全することが中心コンセプト
- OSSとして公開されており、コミュニティがCheckpointの実装に参加できる
- Anthropicの[[Claude Code]]、GoogleのGemini CLIに真っ先に対応し、AIコーディングエコシステムの主要プレイヤーと連携
- $6,000万のシードラウンドはスタートアップ史上最大規模のシードの一つ

## 関連項目

- [[Claude Code]]

## 参考

- [GitHub - entireio/cli](https://github.com/entireio/cli)
- [Former GitHub CEO launches new developer platform - GeekWire](https://www.geekwire.com/2026/former-github-ceo-launches-new-developer-platform-with-huge-60m-seed-round/)
- [Thomas Dohmke interview - The New Stack](https://thenewstack.io/thomas-dohmke-interview-entire/)
- [元GitHub CEOがAI開発プラットフォーム「Entire CLI」をOSS化 - Publickey](https://www.publickey1.jp/blog/26/github_ceoaientire_cligit.html)
