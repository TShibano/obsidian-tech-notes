---
title: "uv"
date: 2026-11-09
tags:
  - Language
  - Python
  - Tool
  - CLI
related:
  - "[[Python]]"
  - "[[ruff]]"
  - "[[ty]]"
  - "[[pytest]]"
  - "[[Docker]]"
---

## 概要

uv は Astral 製の Python パッケージ・プロジェクト管理ツールで，Rust で実装されている．公式ドキュメントは「pip, pip-tools, pipx, poetry, pyenv, twine, virtualenv などを置き換える単一のツール」と位置づけている．

## 詳細

### 主な機能

- プロジェクト管理: `pyproject.toml` と，ユニバーサルなロックファイル `uv.lock` による依存解決
- 単一ファイルスクリプトの実行: 依存関係をスクリプト内に記述できる（PEP 723）
- Python のインストールとバージョン管理（`uv python`）
- Python 製 CLI ツールの実行・インストール（`uvx` / `uv tool`）
- pip 互換インターフェース（`uv pip`）
- Cargo 風のワークスペース，グローバルキャッシュによるディスク節約
- macOS / Linux / Windows 対応

公式は「pip より 10-100 倍高速」と主張している（Trio の依存関係をウォームキャッシュでインストールするベンチマーク．プロジェクト自身の数値）．

### 主要コマンド

| 用途 | コマンド |
|------|----------|
| プロジェクト初期化 | `uv init` |
| 依存追加 | `uv add <pkg>` |
| 環境同期 | `uv sync`（CI や Docker では `--locked`） |
| 実行 | `uv run <cmd>` |
| ロック | `uv lock` |
| Python 管理 | `uv python install` / `uv python upgrade` |
| ツール | `uvx <tool>` / `uv tool install <tool>` |
| スクリプト依存宣言 | `uv add --script <file> <pkg>` |
| pip 互換 | `uv pip install` / `uv pip compile` |

### ロックファイルと再現性

- `uv.lock` はプロジェクトルートに 1 つ置かれ，全プラットフォームを対象とするユニバーサルな形式．
- Docker 等では `uv sync --locked` を使うと，ロックが `pyproject.toml` と食い違っている場合にビルドが失敗する．`--frozen` は `pyproject.toml` を確認せずロックをそのまま使う．
- Docker では依存インストールとプロジェクトインストール（`--no-install-project`）を分けるとレイヤキャッシュが効く．`.venv` は `.dockerignore` に入れる．

### 既存ツールとの比較

| ツール | 役割 | uv での置き換え |
|--------|------|-----------------|
| pip / virtualenv | インストール / 仮想環境 | `uv pip`, `uv venv` |
| pip-tools | requirements のコンパイル | `uv pip compile`, `uv lock` |
| poetry | 依存・環境・ビルド・公開 | `uv init/add/lock/sync/build/publish` |
| pyenv | Python バージョン管理 | `uv python` |
| pipx | CLI ツールの隔離インストール | `uv tool` / `uvx` |

Poetry は Python バージョン管理やグローバル CLI を別ツールに任せる．uv はこれらを内蔵する点が違い．

### Python バージョン管理

uv は必要な Python を自動でダウンロードできる（`uv run`, `uv venv` など）．スクリプトのインラインメタデータには `requires-python` も書ける．

### インラインスクリプトメタデータ

`# /// script` ブロック（TOML）に依存を書く．`uv add --script` で追記でき，`uv run` が環境を自動作成する．`dependencies` は空でも必須．プロジェクト内でもインラインメタデータが優先される．

### ワークスペース

Cargo のワークスペースに着想を得ており，複数パッケージが共通の `uv.lock` を共有する．`uv run` / `uv sync` は既定でルートが対象で，`--package` で特定メンバに絞れる．メンバ間で依存バージョンが衝突する場合は不向きで，path 依存を使う．

### 最新動向（2026-11-09 時点）

- 最新リリースは 0.13.7（2026-11-03）．`uv workspace metadata` に `--output-version` を追加．
- 0.13.0（2026-10-09）: Python 3.15 が既定の安定版に．`--require-hashes` を include された constraints ファイルにも適用，Windows では ARM64 ネイティブ Python を優先，tar 処理を `tar-codec` に変更（キャッシュ形式変更あり）などの破壊的変更．
- 0.12.0（2026-07-28）: プレリリースを推移的依存からも許容する挙動に変更（`--prerelease disallow` で抑止）．
- 0.13.4 / 0.13.5: 侵害された CA 中間証明書への対応．0.13.5 後は `uv python upgrade` が推奨される．
- 2026-03-19，Astral は OpenAI の Codex チームに参加する（OpenAI が Astral を買収する）契約を結んだと発表した．uv / Ruff / ty はオープンソースとして維持するとしている．取引完了の状況は未確認．

## ポイント

- uv 1 つで pip / pip-tools / poetry / pyenv / pipx / virtualenv の用途を概ねカバーできる
- `uv.lock` と `uv sync --locked` で CI・Docker の再現性を担保する
- PEP 723 により 1 ファイルのスクリプトに依存を同梱できる
- 速度の倍率は Astral 自身のベンチマークであり，環境で変わる
- 0.x 系で破壊的変更が入るため，マイナーバージョン更新時はリリースノートを確認する

## 関連項目

- [[Python]]
- [[ruff]]
- [[ty]]
- [[pytest]]
- [[Docker]]

## 参考

- [uv ドキュメント](https://docs.astral.sh/uv/)
- [uv リリース (GitHub)](https://github.com/astral-sh/uv/releases)
- [スクリプトガイド](https://docs.astral.sh/uv/guides/scripts/)
- [ワークスペース](https://docs.astral.sh/uv/concepts/projects/workspaces)
- [Astral to join OpenAI（Astral 公式ブログ）](https://astral.sh/blog/openai)
