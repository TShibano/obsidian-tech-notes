---
title: "Warp"
date: 2026-02-23
tags:
  - Tool
  - CLI
  - AI
  - Agent
related:
  - "[[Alacritty]]"
  - "[[Ghostty]]"
  - "[[WezTerm]]"
  - "[[Claude Code]]"
---

## 概要

Warp はニューヨークのスタートアップ Warp Dev が開発した AI 統合ターミナル。「エージェント型開発環境（Agentic Development Environment）」を標榜し、ターミナルと AI エージェントを融合させた次世代の開発ワークフローを提供する。

## 詳細

### 基本情報

| 項目 | 内容 |
|------|------|
| 開発元 | Warp Dev（CEO: Zach Lloyd） |
| 言語 | Rust |
| レンダリング | GPU アクセラレーション |
| ライセンス | プロプライエタリ（無料プランあり） |
| 対応 OS | macOS、Linux、Windows |
| 位置づけ | AI 統合ターミナル / エージェント型開発環境 |

### 設計思想

Warp は「ターミナルを AI のワークベンチとして再定義する」ビジョンのもとに開発されている。従来のターミナルエミュレータが「コマンドを打ってテキストを表示するもの」であるのに対し、Warp は AI エージェントが実際にコードを書き・実行し・デバッグするための環境として進化している。

### 主要 AI 機能

#### AI コマンド提案

コマンドラインで `#` を入力すると自然言語で操作内容を記述でき、Warp AI がコマンドを提案する。コマンドを暗記する必要がなくなり、「ログを直近 1 時間分だけ確認して」のように自然言語で操作できる。

#### エラー自動解析

コンパイルエラーやコマンド失敗時に自動でエラーを解析し、修正案を提示する。依存関係の不足・バージョン競合なども検出する。

#### Agents 3.0

Warp が 2026 年に投入した「Agents 3.0」は、ターミナルに対して完全な操作権限を持つ AI エージェント。インタラクティブなターミナルコマンドの実行（Full Terminal Use）と、変更内容の自動検証（Computer Use）が可能。

#### マルチエージェント実行

Warp は複数の AI エージェントを並行して実行できる。Warp 独自のエージェントに加え、[[Claude Code]]・Codex・Gemini CLI を同一ターミナル上で並列実行・管理できる。

### AI モデル

Warp はタスクの種類に応じて Anthropic・OpenAI・Google（Gemini）のモデルを使い分けるルーティングを行っている：

- コーディング: Anthropic Claude 系
- Diff 適用: 専用モデル
- 予測・提案: 軽量モデル

### 通常のターミナル機能

AI 機能に加えて、モダンなターミナルとしての基本機能も充実：

- ブロック単位の入出力（コマンドと出力をセットで管理）
- 複数タブ・ペイン分割
- コマンド履歴の検索・再利用
- チームへのコマンド・ワークフロー共有

### 競合との位置づけ

| ターミナル | AI 機能 | エージェント | 用途 |
|-----------|---------|------------|------|
| **Warp** | 深い統合 | あり（Agents 3.0） | AI-first 開発環境 |
| [[Ghostty]] | なし | なし | 高性能・軽量端末 |
| [[WezTerm]] | なし | なし | 高機能マルチプレクサ |
| [[Alacritty]] | なし | なし | 最速・最軽量 |

## ポイント

- 「エージェント型開発環境」として AI とターミナルを深く統合
- `#` コマンドで自然言語→コマンド変換、エラー自動解析
- Agents 3.0 でターミナルのフル操作権限を持つ AI エージェントを実現
- Claude Code、Codex、Gemini CLI を並列管理する統合ハブとしても機能
- 複数 AI モデルをタスク別に自動ルーティング

## 関連項目

- [[Alacritty]] - Rust 製、シンプル・高速ターミナル
- [[Ghostty]] - Zig 製、ネイティブ UI ターミナル
- [[WezTerm]] - Rust 製、マルチプレクサ内蔵ターミナル
- [[Claude Code]] - Warp 上で実行可能な AI コーディングエージェント

## 参考

- [Warp 公式サイト](https://www.warp.dev/)
- [Warp AI Features](https://www.warp.dev/warp-ai)
- [Warp All Features](https://www.warp.dev/all-features)
- [GitHub - warpdotdev/Warp](https://github.com/warpdotdev/Warp)
