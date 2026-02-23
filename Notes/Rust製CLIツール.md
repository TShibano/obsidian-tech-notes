---
title: "Rust製CLIツール"
date: 2026-02-23
tags:
  - Tool
  - CLI
  - Language
  - Rust
related:
  - "[[Rust]]"
  - "[[Go製CLIツール]]"
  - "[[Alacritty]]"
  - "[[Zellij]]"
  - "[[git-delta]]"
  - "[[Helix]]"
---

## 概要

[[Rust]] の安全性・高速性・クロスプラットフォーム性を活かした CLI ツール群が 2020 年代以降に急増し、伝統的な Unix ツール（ls, cat, grep, find, du 等）の現代的代替として広く採用されている。「Rewritten in Rust（RiR）」と呼ばれるこの潮流は、開発者体験の向上・パフォーマンス改善・デフォルト設定の洗練を特徴とする。

## 詳細

### ファイル操作系

#### eza — ls の現代的代替

```bash
eza --icons --git --long
```

- **前身**: `exa`（開発停止）の継続プロジェクト
- **特徴**: カラー出力、アイコン表示、Git ステータス表示、ツリー表示（`--tree`）
- **インストール**: `brew install eza` / `cargo install eza`

#### bat — cat の現代的代替

```bash
bat README.md
```

- **特徴**: シンタックスハイライト、行番号表示、Git 差分表示、自動ページング
- **git-delta との連携**: [[git-delta]] がデフォルトのシンタックステーマ・言語定義を bat から流用
- **インストール**: `brew install bat` / `cargo install bat`

#### fd — find の現代的代替

```bash
fd '\.md$' ./Notes
```

- **特徴**: 直感的なシンタックス、`.gitignore` を自動的に尊重、デフォルトでカラー出力
- **速度**: GNU find より平均 3〜5 倍高速（並列化）
- **インストール**: `brew install fd` / `cargo install fd-find`

#### dust — du の現代的代替

```bash
dust -r ./
```

- **特徴**: ディスク使用量をツリー形式＋バーグラフで視覚的に表示
- **インストール**: `brew install dust` / `cargo install du-dust`

### 検索系

#### ripgrep（rg）— grep の現代的代替

```bash
rg 'TODO' --type py
```

- **特徴**: GNU grep より 5〜10 倍高速、`.gitignore` 尊重、Unicode 対応
- **採用実績**: VS Code が標準検索エンジンとして採用
- **インストール**: `brew install ripgrep` / `cargo install ripgrep`

### ナビゲーション・シェル系

#### zoxide — cd の現代的代替

```bash
z proj   # 過去に訪問した "proj" を含むディレクトリにジャンプ
```

- **特徴**: ディレクトリ訪問履歴を学習し、部分一致でジャンプ
- **shell 統合**: bash, zsh, fish, PowerShell 対応
- **インストール**: `brew install zoxide` / `cargo install zoxide`

#### starship — クロスシェルプロンプト

```toml
# ~/.config/starship.toml の例
[git_branch]
symbol = " "
```

- **特徴**: Git ブランチ・言語バージョン・クラウドコンテキストを条件付き表示
- **shell 対応**: bash, zsh, fish, PowerShell, Nushell 等
- **速度**: 各種プロンプトプラグインより高速（Rust の並列実行）
- **インストール**: `brew install starship`

### システム監視系

#### bottom（btm）— top/htop の現代的代替

```bash
btm
```

- **特徴**: CPU・メモリ・ネットワーク・ディスク・プロセスを TUI でリアルタイム表示
- **インストール**: `brew install bottom` / `cargo install bottom`

### まとめ対応表

| 元ツール | Rust 代替 | 主な改善点 |
|----------|-----------|-----------|
| `ls` | `eza` | アイコン、Git 状態、カラー |
| `cat` | `bat` | シンタックスハイライト、行番号、Git diff |
| `find` | `fd` | シンプルな構文、.gitignore 対応、高速 |
| `grep` | `ripgrep`（rg） | 高速、Unicode、.gitignore 対応 |
| `du` | `dust` | ツリー表示、バーグラフ |
| `cd` | `zoxide` | 訪問履歴学習、部分一致ジャンプ |
| `top` | `bottom`（btm） | TUI、複数メトリクス |
| — | `starship` | クロスシェルプロンプト |

### その他の注目ツール

| ツール | 説明 |
|--------|------|
| [[git-delta]] | git diff のシンタックスハイライトページャー |
| [[Alacritty]] | GPU アクセラレーション付きターミナルエミュレータ |
| [[Zellij]] | ターミナルマルチプレクサ |
| [[Helix]] | モーダルテキストエディタ |
| `tokei` | コード行数カウンター（言語別、超高速） |
| `hyperfine` | コマンドラインベンチマーク |
| `xh` | HTTPie の Rust 代替（HTTP クライアント） |

## ポイント

- Rust の安全性・高速性・クロスプラットフォーム対応が CLI ツール開発に適している
- 単なる高速化ではなく、デフォルト設定の改善・UX の洗練が特徴
- VS Code（ripgrep）を筆頭に、メジャーなエコシステムへの採用が進む
- eza, bat, fd, ripgrep はほぼ既存ツールのドロップイン代替として機能
- zoxide と starship はシェル統合型で、プラットフォームを問わず同じ体験を提供

## 関連項目

- [[Rust]] - これらツールの実装言語
- [[Go製CLIツール]] - 別言語（Go）による CLI ツール群
- [[Alacritty]] - Rust 製ターミナルエミュレータ
- [[Zellij]] - Rust 製ターミナルマルチプレクサ
- [[git-delta]] - Rust 製 git diff ページャー
- [[Helix]] - Rust 製モーダルテキストエディタ

## 参考

- [GitHub - sts10/rust-command-line-utilities](https://github.com/sts10/rust-command-line-utilities)
- [14 Rust Tools for Linux Terminal Dwellers - It's FOSS](https://itsfoss.com/rust-cli-tools/)
- [Rewritten in Rust: Modern Alternatives - Zaiste Programming](https://zaiste.net/posts/shell-commands-rust/)
- [CLI++: Upgrade Your Command Line - KDAB](https://www.kdab.com/cli-upgrade-your-command-line-with-a-new-generation-of-everyday-tools/)
