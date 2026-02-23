---
title: "git-delta"
date: 2026-02-23
tags:
  - Tool
  - CLI
  - Git
related:
  - "[[Rust]]"
  - "[[jujutsu]]"
  - "[[Rust製CLIツール]]"
---

## 概要

git-delta（コマンド名: `delta`）は Dan Davison が Rust で開発した、git・diff・grep・blame 出力のシンタックスハイライトページャー。`bat` と同じテーマセットを使用した美しい差分表示を提供し、`~/.gitconfig` の `core.pager` に設定するだけで既存の Git ワークフローに即座に統合できる。

## 詳細

### 基本情報

| 項目 | 内容 |
|------|------|
| 開発者 | Dan Davison |
| 言語 | [[Rust]] |
| パッケージ名 | `git-delta`（コマンド: `delta`） |
| ライセンス | MIT |
| GitHub | github.com/dandavison/delta |

### インストール

```bash
# macOS
brew install git-delta

# Cargo (Rust)
cargo install git-delta

# Ubuntu / Debian
apt install git-delta
```

### 設定（~/.gitconfig）

```gitconfig
[core]
    pager = delta

[interactive]
    diffFilter = delta --color-only

[delta]
    navigate = true    # n/N でファイル間を移動
    dark = true        # ダークテーマ（light = true も可）
    side-by-side = true  # 並列表示

[merge]
    conflictStyle = zdiff3
```

### 主要機能

#### シンタックスハイライト

`bat` と同一のシンタックスハイライトテーマを使用（`bat` の `.tmTheme` カスタムテーマも自動認識）。プログラミング言語のコード差分が見やすくなる。

```bash
# 利用可能なテーマ一覧
delta --list-syntax-themes

# 利用可能な言語一覧
delta --list-languages
```

#### 単語レベルの差分

Levenshtein 編集推論アルゴリズムを用いた単語レベル（word-level）の差分強調表示。行全体ではなく、変更された単語だけをハイライトするため、微細な変更を素早く把握できる。

#### 並列表示（Side-by-side）

`side-by-side = true` で変更前・変更後を左右に並べて表示。デフォルトで行番号も表示される。

#### ナビゲーション

- `n` / `N`: 大きな diff 内でファイル間を移動（`navigate = true` 設定時）
- `git log -p` ビューでは各コミットの diff 間を移動

#### その他の機能

- `git blame` のシンタックスハイライト
- `--hyperlinks` オプションで GitHub/GitLab/Bitbucket のコミットリンクを生成
- `git --color-moved` 対応（移動されたコードブロックの特別表示）
- `-/+` マーカーをデフォルトで非表示にし、diff からのコピーを容易に

### 使用例

```bash
# 通常の diff（delta が自動的に介入）
git diff
git show
git log -p
git blame README.md

# 直接使用
diff -u a.txt b.txt | delta
```

### テーマ設定例

```gitconfig
[delta]
    syntax-theme = Dracula
    # または
    syntax-theme = OneHalfDark
    # または
    syntax-theme = gruvbox-dark
```

## ポイント

- `~/.gitconfig` の設定だけで既存 git コマンド（diff, show, log, blame）すべてに適用
- `bat` のテーマと言語定義を共有するため、カスタマイズが統一的
- 単語レベルの差分で変更箇所を正確に把握
- Side-by-side 表示でコードレビューの効率が向上
- `n`/`N` ナビゲーションで大きな差分内を素早く移動

## 関連項目

- [[Rust]] - delta の実装言語
- [[jujutsu]] - Rust 製 Git 互換バージョン管理システム（delta と組み合わせ可能）
- [[Rust製CLIツール]] - 同じく Rust 製の CLI ツール群

## 参考

- [GitHub - dandavison/delta](https://github.com/dandavison/delta)
- [delta 公式ドキュメント](https://dandavison.github.io/delta/introduction.html)
- [delta Configuration](https://dandavison.github.io/delta/configuration.html)
