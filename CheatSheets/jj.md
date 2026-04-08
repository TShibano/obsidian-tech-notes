---
title: "jj Cheatsheet"
date: 2026-04-08
tags:
  - Cheatsheet
  - Tool
  - Git
related:
  - "[[jujutsu]]"
---

# 🔧 jj Cheatsheet

> **jj (Jujutsu)** — Git 互換の次世代バージョン管理 CLI。ステージング不要・自動スナップショット・ファーストクラスのコンフリクト。
>
> 📖 [Official Docs](https://docs.jj-vcs.dev/latest/) · 📦 `v0.34`

---

## Install / Setup

| Command | Description |
| :-- | :-- |
| `brew install jj` | macOS Homebrew でインストール |
| `cargo install --locked --bin jj jj-cli` | cargo からビルド |
| `jj config set --user user.name "<name>"` | ユーザー名設定 |
| `jj config set --user user.email "<email>"` | メール設定 |
| `jj config set --user ui.editor "<editor>"` | デフォルトエディタ設定 |
| `jj config edit --user` | `~/.config/jj/config.toml` を直接編集 |

## Repository

| Command | Description |
| :-- | :-- |
| `jj git init` | 新規 jj リポジトリを作成 |
| `jj git init --colocate` | 既存 `.git` と共存（colocated）で初期化 |
| `jj git clone <url>` | リモートをクローン |
| `jj git clone --colocate <url>` | colocated でクローン |

## Status & Log

| Command | Description |
| :-- | :-- |
| `jj st` / `jj status` | 現在の change と変更ファイルを表示 |
| `jj log` | リビジョングラフ表示 |
| `jj log -r <rev>` | リビジョン指定（revset） |
| `jj log -r 'all()'` | 全 change を表示 |
| `jj show <rev>` | 指定 change の詳細・差分 |
| `jj diff` | working copy の差分 |
| `jj diff --from <a> --to <b>` | 任意 2 リビジョン間の差分 |
| `jj evolog` | 単一 change の履歴（書き換え遍歴） |

## Change 操作

> jj では「コミット」を **change** と呼び、working copy 自体が常に change として扱われる。

| Command | Description |
| :-- | :-- |
| `jj new` | 新規 change を作成して移動 |
| `jj new -m "<msg>"` | メッセージ付きで新規 change |
| `jj new <rev>` | 指定 rev の上に新規 change |
| `jj new <a> <b>` | 複数親を持つマージ change を作成 |
| `jj desc -m "<msg>"` | 現 change の説明を変更 |
| `jj describe` | エディタで説明編集 |
| `jj commit -m "<msg>"` | 現 change を確定し新規 change へ |
| `jj edit <rev>` | 既存 change を working copy として開く |
| `jj abandon <rev>` | change を破棄（子は再リベース） |
| `jj duplicate <rev>` | change を複製 |

## Squash / Split / Move

| Command | Description |
| :-- | :-- |
| `jj squash` | 現 change を親に統合 |
| `jj squash -i` | 対話的に一部のみ squash |
| `jj squash --into <rev>` | 任意の change へ統合 |
| `jj split` | 現 change を 2 つに分割 |
| `jj split -i` | 対話的分割 |
| `jj move --from <a> --to <b>` | 変更を別 change へ移動 |

## Rebase

| Command | Description |
| :-- | :-- |
| `jj rebase -d <dest>` | 現 change を `<dest>` 上にリベース |
| `jj rebase -s <src> -d <dest>` | `<src>` 以下を移動 |
| `jj rebase -b <branch> -d <dest>` | branch 全体を移動 |
| `jj rebase -r <rev> -d <dest>` | 単一 change のみ移動 |

## Bookmark（≒ Git ブランチ）

> ブックマークは自動前進しないので明示的に `set` する。

| Command | Description |
| :-- | :-- |
| `jj bookmark list` | ブックマーク一覧 |
| `jj bookmark create <name>` | 現 change にブックマーク作成 |
| `jj bookmark create <name> -r <rev>` | 指定 rev にブックマーク作成 |
| `jj bookmark set <name>` | 現 change へ移動 |
| `jj bookmark set <name> -r <rev> --allow-backwards` | 後退も許可 |
| `jj bookmark delete <name>` | 削除 |
| `jj bookmark rename <old> <new>` | リネーム |
| `jj bookmark track <name>@<remote>` | リモートブックマークを追跡 |

## Git 連携

| Command | Description |
| :-- | :-- |
| `jj git fetch` | リモートから取得 |
| `jj git fetch --remote <name>` | リモート指定 |
| `jj git push` | 追跡中ブックマークを push |
| `jj git push --bookmark <name>` | 個別ブックマークを push |
| `jj git push --all` | 全ブックマーク push |
| `jj git push -c @` | 現 change を新ブックマークで push |
| `jj git remote add <name> <url>` | リモート追加 |
| `jj git remote list` | リモート一覧 |

## Conflict 操作

> jj はコンフリクトを change に保存できる。後から `jj resolve` で解消可能。

| Command | Description |
| :-- | :-- |
| `jj resolve` | マージツールでコンフリクト解消 |
| `jj resolve --list` | 未解消ファイル一覧 |
| `jj restore --from <rev>` | 指定 rev の内容で復元 |

## Operation Log / Undo

| Command | Description |
| :-- | :-- |
| `jj op log` | 操作履歴を表示 |
| `jj undo` | 直前の操作を取り消し |
| `jj op restore <op>` | 任意の操作時点へ復元 |
| `jj op abandon <op>` | 操作履歴のエントリを破棄 |

## File 操作

| Command | Description |
| :-- | :-- |
| `jj file list` | 追跡ファイル一覧 |
| `jj file show <path>` | ファイル内容表示 |
| `jj file annotate <path>` | `git blame` 相当（v0.24+） |
| `jj file track <path>` | 明示的に追跡開始 |
| `jj file untrack <path>` | 追跡解除 |

## Revset（リビジョン式）

> リビジョン指定はすべて revset 言語で書ける。`jj log -r '<expr>'` で試せる。

| Expression | Description |
| :-- | :-- |
| `@` | 現在の change（working copy） |
| `@-` | 親 change |
| `@+` | 子 change |
| `<id>` | change ID または commit ID prefix |
| `trunk()` | trunk ブランチの先頭 |
| `mine()` | 自分が author の change |
| `bookmarks()` | ブックマークが指す change |
| `heads(all())` | すべての可視ヘッド |
| `<a>..<b>` | a の子孫かつ b の祖先 |
| `<a> | <b>` | 和集合 |
| `<a> & <b>` | 積集合 |
| `~<a>` | 補集合 |
| `description("fix")` | 説明文に "fix" を含む |

## Workflows / Recipes

<details>
<summary><b>📌 GitHub に PR を作る基本フロー</b></summary>

```sh
jj git fetch                          # リモート最新を取得
jj new trunk()                        # trunk の上に新規 change
# ファイルを編集...
jj desc -m "feat: add awesome thing"  # 説明を付ける
jj bookmark create my-feature -r @    # ブックマーク作成
jj git push --bookmark my-feature     # GitHub に push → PR 作成
```

</details>

<details>
<summary><b>📌 大きい change を分割する</b></summary>

```sh
jj split -i              # 対話的に変更を選択し 2 つの change に分割
jj log                   # 分割結果を確認
jj desc -r @-- -m "..."  # 古い側の説明を編集
```

</details>

<details>
<summary><b>📌 履歴の途中の change を修正する</b></summary>

```sh
jj edit <change-id>      # 過去 change を working copy として開く
# ファイル修正...
jj new <child-id>        # 子 change へ戻る（自動リベース済み）
```

</details>

<details>
<summary><b>📌 やらかした操作を取り消す</b></summary>

```sh
jj op log                # 操作履歴を確認
jj undo                  # 直前の操作を取り消し
jj op restore <op-id>    # 任意の時点へ巻き戻し
```

</details>

<details>
<summary><b>📌 コンフリクトを後回しにしてリベース</b></summary>

```sh
jj rebase -d main           # コンフリクトしても change として保存される
jj log                      # コンフリクトマーカー付き change を確認
jj resolve                  # 都合のよいタイミングで解消
jj squash                   # 解消結果を元 change に統合
```

</details>

<details>
<summary><b>📌 見やすいログ用エイリアス</b></summary>

```toml
# ~/.config/jj/config.toml
[aliases]
lg = ["log", "-T", "change_id.short() ++ \" \" ++ \"(\" ++ author.timestamp().format(\"%Y-%m-%d %H:%M\") ++ \")\" ++ \" \" ++ description.first_line() ++ if(bookmarks, \" [\" ++ bookmarks.join(\", \") ++ \"]\", \"\") ++ \" \" ++ commit_id.short()"]
```

</details>

## Tips & Gotchas

- 💡 すべての操作は `jj undo` で取り消せる。安心して試行錯誤できる。
- 💡 `jj` は `git add` 不要。ファイル編集は自動で working copy change に反映される。
- 💡 既存 Git リポジトリには `jj git init --colocate` で導入でき、Git と併用可能。
- 💡 リビジョン指定はどこでも revset が使える。`jj log -r 'mine() & ~bookmarks()'` で名無しの自分の change を一覧。
- ⚠️ ブックマークは **自動で前進しない**。push 前に `jj bookmark set <name>` で動かす必要がある。
- ⚠️ `jj abandon` は子 change を自動リベースする。意図せず履歴が変わることに注意。
- ⚠️ Colocated リポジトリで Git コマンドと jj を混在させると稀に状態が壊れる。基本どちらか一方から操作する。
- ⚠️ `commit_id` は書き換えで変わるが、`change_id` は不変。スクリプトでは change_id を使う。

## See Also

- [[jujutsu]] — jj の概念・背景・Git との比較を解説した技術ノート
- [[git-delta]] — 差分表示を見やすくする pager
- [Jujutsu docs](https://docs.jj-vcs.dev/latest/) — 公式ドキュメント
- [Git comparison](https://docs.jj-vcs.dev/latest/git-comparison/) — Git ユーザー向け対応表
- [Steve's Jujutsu Tutorial](https://steveklabnik.github.io/jujutsu-tutorial/) — 入門チュートリアル

---

<sub>Last updated: 2026-04 · License: Apache-2.0</sub>
