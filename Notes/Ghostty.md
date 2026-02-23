---
title: "Ghostty"
date: 2026-02-23
tags:
  - Tool
  - CLI
related:
  - "[[Alacritty]]"
  - "[[WezTerm]]"
  - "[[Warp]]"
  - "[[Zellij]]"
  - "[[Zig]]"
---

## 概要

Ghostty は Mitchell Hashimoto（HashiCorp 共同創業者）が Zig で開発した、高速・高機能・クロスプラットフォームなターミナルエミュレータ。プラットフォームネイティブ UI と GPU アクセラレーションを両立し、2024年12月に 1.0 を公開した。

## 詳細

### 基本情報

| 項目 | 内容 |
|------|------|
| 開発者 | Mitchell Hashimoto |
| 言語 | Zig（コア: libghostty）、Swift/AppKit（macOS GUI）、GTK4（Linux GUI） |
| レンダリング | Metal（macOS）、OpenGL（Linux） |
| ライセンス | MIT |
| 安定版 | 1.0（2024年12月リリース） |
| 対応 OS | macOS、Linux |

### 設計思想

Ghostty のコア哲学は「高機能でありながらネイティブに振る舞う」こと。他の多くのターミナルが Electron やカスタム描画に頼る中、Ghostty は各 OS のネイティブ UI ツールキット（macOS: Swift/AppKit、Linux: GTK4）を使用するため、OS のアクセシビリティ機能や IME との統合が自然に機能する。

コアロジックは Zig で書かれた共有ライブラリ「libghostty」として実装されており、各プラットフォームの GUI がこれを呼び出す構造。

### 主要機能

#### タブ・ペイン・マルチウィンドウ

ターミナルエミュレータ単体でタブ、ペイン分割、複数ウィンドウをサポート。ターミナルマルチプレクサ（[[Zellij]] や tmux）なしで完結したワークフローが可能。

#### ターミナル互換性

xterm のエスケープシーケンスを他のターミナルより広くサポート。Kitty グラフィクスプロトコル・Kitty キーボードプロトコル・同期描画なども対応しており、ターミナルアプリ開発者にとっても魅力的。

#### ネイティブ統合（macOS）

- Quick Look、Force Touch 対応
- macOS セキュア入力 API 対応
- 再起動時のウィンドウ状態復元
- ライト/ダークモード自動切替通知

#### 設定

設定ファイルなしでも動作するほどデフォルトが整っている。設定ファイルは `~/.config/ghostty/config`。

### バージョン履歴

| バージョン | 主な変更 |
|-----------|---------|
| 1.0（2024年12月） | 初の安定版公開。約2年のベータテスト経て |
| 1.1〜1.2 | バグ修正・安定化 |
| 1.3（予定） | スクロールバー追加、コマンドパレットにセッション検索、Linux タブリネーム |

### 他ターミナルとの比較

| ターミナル | 言語 | タブ/分割 | AI 統合 | ネイティブ UI |
|-----------|------|----------|---------|-------------|
| **Ghostty** | Zig | あり | なし | macOS・Linux 両方 |
| [[Alacritty]] | Rust | なし（マルチプレクサ前提） | なし | なし（OpenGL） |
| [[WezTerm]] | Rust | あり | なし | なし（OpenGL） |
| [[Warp]] | Rust | あり | あり（AI エージェント） | なし |

## ポイント

- HashiCorp 共同創業者 Mitchell Hashimoto が個人プロジェクトとして Zig を学ぶ目的で開始
- Zig 製コア（libghostty）＋ネイティブ UI の組み合わせが他ターミナルにない特徴
- 高機能（タブ・ペイン内蔵）でありながら、ネイティブ統合を犠牲にしない設計
- 2024年末 1.0 公開後も活発に開発継続（1.3 でスクロールバー追加予定）

## 関連項目

- [[Alacritty]] - Rust 製 GPU ターミナル（シンプル志向）
- [[WezTerm]] - Rust 製、マルチプレクサ機能内蔵
- [[Warp]] - AI 統合ターミナル
- [[Zellij]] - Rust 製ターミナルマルチプレクサ
- [[Zig]] - Ghostty のコアを書いた言語

## 参考

- [Ghostty 公式サイト](https://ghostty.org/)
- [GitHub - ghostty-org/ghostty](https://github.com/ghostty-org/ghostty)
- [Ghostty 1.0 リリース振り返り - Mitchell Hashimoto](https://mitchellh.com/writing/ghostty-1-0-reflection)
- [Ghostty features ドキュメント](https://ghostty.org/docs/features)
