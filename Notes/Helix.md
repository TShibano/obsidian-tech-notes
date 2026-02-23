---
title: "Helix"
date: 2026-02-23
tags:
  - Tool
  - CLI
  - Editor
related:
  - "[[Rust]]"
  - "[[Rust製CLIツール]]"
---

## 概要

Helix（helix-editor）は Rust で書かれたポストモダンなモーダルテキストエディタ。Vim/Kakoune から着想を得ながらも「選択→アクション」モデルを採用し、Tree-sitter による高精度なシンタックスハイライトと LSP によるコード補完・診断を標準で内蔵する。インストール直後から IDE に近い開発体験が得られる。

## 詳細

### 基本情報

| 項目 | 内容 |
|------|------|
| 言語 | [[Rust]] |
| ライセンス | MPL-2.0 |
| 最新安定版 | 25.07（2025年7月リリース） |
| 設定形式 | TOML |
| 対応 OS | Linux、macOS、Windows |
| GitHub | github.com/helix-editor/helix |

### 設計思想：選択→アクションモデル

Vim の「アクション→モーション」（例: `dw` = delete word）と異なり、Helix は **「選択→アクション」** を採用している。

| 操作 | Vim | Helix |
|------|-----|-------|
| 1 単語削除 | `dw`（delete, then word） | `wd`（select word, then delete） |
| 行末まで変更 | `c$` | `Glc`（select to end, change） |

この設計により「今何を選択しているか」が常に視覚的にわかり、操作の意図が明確になる。Kakoune も同様のモデルを採用。

### モーダル編集

Helix は複数のモードを持つ：

| モード | 説明 |
|--------|------|
| Normal | ナビゲーション・選択・コマンド |
| Insert | テキスト入力 |
| Select | 選択範囲の拡張 |
| Command | Ex コマンド入力 |

### 複数選択（Multiple Selections）

複数箇所を同時に選択して編集できる機能。Helix の核心的な機能の一つ。

```
# 複数選択の例
%    # ファイル全体を選択
s    # 選択内で検索→マッチ部分が複数選択になる
c    # 選択範囲をすべて変更（インサートモードへ）
```

### LSP 統合

Language Server Protocol（LSP）を標準で内蔵。インストール直後から以下の機能が使える（LSP サーバーは別途インストールが必要）：

- コード補完（オートコンプリート）
- インラインエラー・警告表示
- 定義ジャンプ（`gd`）
- 参照一覧（`gr`）
- ホバードキュメント（`K`）
- フォーマット（`<space>f`）
- リネーム（`<space>r`）

**v23.10 以降**: 同一言語に対して複数の LSP サーバーを設定可能。

### Tree-sitter による構文解析

Tree-sitter を使用した増分構文解析により、高速かつ正確なシンタックスハイライトを実現。ネストした言語（HTML 内の JavaScript など）にも対応。

### パスコンプリート（v25.01 以降）

LSP に依存しないファイルパス補完を内蔵。カーソル位置の文字列からパスを自動検出し、絶対パス/相対パスを文脈に応じて補完する。

### 設定

```toml
# ~/.config/helix/config.toml
theme = "catppuccin_mocha"

[editor]
line-number = "relative"
mouse = false
auto-save = false

[editor.cursor-shape]
normal = "block"
insert = "bar"

[keys.normal]
"j" = "move_char_down"
```

### バージョン履歴

| バージョン | リリース | 主な変更 |
|-----------|---------|---------|
| 25.07 | 2025年7月 | コアコンポーネント刷新、195名のコントリビュータ |
| 25.01 | 2025年1月 | パスコンプリート追加、171名のコントリビュータ |
| 23.10 | 2023年10月 | 1言語に複数 LSP サーバーをサポート |

## ポイント

- 設定不要で LSP・Tree-sitter 統合が使える（プラグイン不要）
- Vim の「アクション→モーション」ではなく「選択→アクション」という独自モデル
- 複数選択機能により、繰り返し編集操作を大幅に削減
- Rust 製で高速。設定は TOML のみ（プラグインシステムなし）
- v25.01 でファイルパス補完を追加、IDE ライクな使いやすさが向上

## 関連項目

- [[Rust]] - Helix の実装言語
- [[Rust製CLIツール]] - 同じく Rust 製の CLI ツール群

## 参考

- [Helix 公式サイト](https://helix-editor.com/)
- [GitHub - helix-editor/helix](https://github.com/helix-editor/helix)
- [Helix Keymap ドキュメント](https://docs.helix-editor.com/master/keymap.html)
- [Release 25.07 Highlights](https://helix-editor.com/news/release-25-07-highlights/)
- [Release 25.01 Highlights](https://helix-editor.com/news/release-25-01-highlights/)
