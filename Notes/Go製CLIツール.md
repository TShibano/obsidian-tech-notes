---
title: "Go製CLIツール"
date: 2026-02-23
tags:
  - Tool
  - CLI
  - Language
  - Go
related:
  - "[[Rust製CLIツール]]"
  - "[[jujutsu]]"
---

## 概要

Go（Golang）はシングルバイナリへのコンパイル・高速な起動・クロスプラットフォーム対応を特徴とし、CLI ツール・TUI（Terminal UI）アプリの開発に広く採用されている。GitHub CLI・lazygit・k9s など、インフラ・開発ワークフローの中核を担う著名ツールが Go で書かれている。

## 詳細

### なぜ Go が CLI に向くか

| 特徴 | 説明 |
|------|------|
| シングルバイナリ | 依存なし。コピーするだけで動く |
| クロスコンパイル | `GOOS=linux GOARCH=amd64 go build` で他 OS 向けをビルド |
| 起動速度 | JVM や Python より大幅に速い |
| goroutine | 軽量な並行処理でネットワーク I/O を効率化 |
| 豊富なライブラリ | cobra（CLI）、bubbletea（TUI）など充実 |

### ファジー検索・インタラクティブ系

#### fzf — インタラクティブファジーファインダー

```bash
# コマンド履歴をファジー検索
history | fzf

# ファイルをプレビュー付きで検索
fzf --preview 'bat --color=always {}'

# vim でファジー検索したファイルを開く
vim $(fzf)
```

- **開発者**: Junegunn Choi
- **特徴**: あらゆるリスト（ファイル・履歴・プロセス・Git コミット等）をインタラクティブにフィルタリング
- **shell 統合**: `Ctrl+R`（履歴検索）・`Ctrl+T`（ファイル検索）を自動設定
- **インストール**: `brew install fzf`

### Git 系

#### lazygit — Git TUI クライアント

```bash
lazygit
```

- **開発者**: Jesse Duffield
- **特徴**: ブランチ・コミット・diff・ステージングをキーボードだけで直感的に操作
- **ビジュアル**: カラー差分・グラフ表示
- **インストール**: `brew install lazygit`

### Kubernetes 系

#### k9s — Kubernetes TUI

```bash
k9s
```

- **特徴**: クラスタのリソースをリアルタイムで監視・操作。Pod のログ閲覧・削除・exec が TUI 上で完結
- **ナビゲーション**: Vim ライクなキーバインド
- **インストール**: `brew install k9s`

### GitHub 連携

#### gh — GitHub 公式 CLI

```bash
gh pr create --title "Add feature X" --body "..."
gh issue list
gh run watch
```

- **開発元**: GitHub（Microsoft）
- **特徴**: PR・Issue・Actions をターミナルから操作。ブラウザ不要の GitHub ワークフロー
- **認証**: `gh auth login` でトークン管理
- **インストール**: `brew install gh`

### テキスト処理系

#### glow — Markdown ターミナルレンダラー

```bash
glow README.md
glow -p  # ページャーモード
```

- **開発元**: Charm（TUI フレームワークも提供）
- **特徴**: Markdown をターミナルで美しくレンダリング。README 確認に便利
- **インストール**: `brew install glow`

#### yq — YAML/JSON/TOML プロセッサ

```bash
yq '.metadata.name' deployment.yaml
yq -o json . config.yaml  # YAML → JSON 変換
```

- **特徴**: `jq` の YAML 版。YAML・JSON・TOML・XML を変換・フィルタリング
- **インストール**: `brew install yq`

### ファイルマネージャー

#### lf — TUI ファイルマネージャー

```bash
lf
```

- **特徴**: ranger（Python）にインスパイアされた軽量 TUI ファイルマネージャー
- **カスタマイズ**: シェルコマンドをキーバインドに割り当て可能
- **インストール**: `brew install lf`

### ツール一覧

| ツール | 分野 | 説明 |
|--------|------|------|
| `fzf` | 検索 | インタラクティブファジーファインダー |
| `lazygit` | Git | Git 操作 TUI |
| `k9s` | Kubernetes | Kubernetes クラスタ TUI |
| `gh` | GitHub | GitHub 公式 CLI |
| `glow` | Markdown | Markdown ターミナルレンダラー |
| `yq` | テキスト処理 | YAML/JSON/TOML プロセッサ |
| `lf` | ファイル | TUI ファイルマネージャー |
| `delta`（git-delta） | Git | diff シンタックスハイライター（Rust 製） |

### Charm エコシステム

Charm は Go で TUI アプリを開発するためのライブラリ群を提供しており、多くの Go 製 CLI ツールが採用：

- **Bubble Tea**: TUI フレームワーク（Elm Architecture）
- **Lip Gloss**: スタイリング
- **Wish**: SSH アプリサーバー

Glow も Charm が開発したツールの一つ。

### [[Rust製CLIツール]] との比較

| 観点 | Go 製 | Rust 製 |
|------|--------|---------|
| 実行速度 | 速い | 最速クラス |
| メモリ効率 | GC あり | GC なし（最小） |
| クロスコンパイル | 非常に簡単 | やや複雑 |
| 開発速度 | 速い | 遅め（型システムが厳格） |
| TUI エコシステム | Bubble Tea（充実） | Ratatui（成長中） |

## ポイント

- シングルバイナリ配布・クロスコンパイルのしやすさが Go を CLI 開発の第一選択肢に
- fzf・lazygit・k9s・gh はそれぞれのカテゴリで事実上の標準ツールの地位
- Charm エコシステムにより、TUI アプリの開発も容易になっている
- goroutine による並行 I/O 処理が、ネットワーク通信を含む CLI ツールに適する

## 関連項目

- [[Rust製CLIツール]] - Rust 製の CLI ツール群
- [[jujutsu]] - Rust 製 Git 互換バージョン管理システム

## 参考

- [awesome-go-cli - GitHub](https://github.com/mantcz/awesome-go-cli)
- [10 Go-Based CLI Tools - Medium](https://medium.com/@neerupujari5/10-go-based-cli-tools-so-good-i-forgot-bash-exists-335b693b6546)
- [Essential CLI/TUI Tools for Developers - freeCodeCamp](https://www.freecodecamp.org/news/essential-cli-tui-tools-for-developers/)
- [My List of CLI Gems - dlvhdr.me](https://www.dlvhdr.me/posts/cli-tools)
