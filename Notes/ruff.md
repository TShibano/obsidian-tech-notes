---
title: "ruff"
date: 2026-11-09
tags:
  - Language
  - Python
  - Tool
  - CLI
related:
  - "[[Python]]"
  - "[[uv]]"
  - "[[ty]]"
  - "[[pytest]]"
  - "[[Rust製CLIツール]]"
  - "[[Rust]]"
  - "[[TOML]]"
---

## 概要

ruff は Astral が開発する，Rust 製の Python linter 兼 formatter．Flake8（とそのプラグイン），Black，isort，pydocstyle，pyupgrade，autoflake などを単一のツールで置き換えることを目的とし，公式ドキュメントは既存ツールより 10〜100 倍高速と謳う．

## 詳細

### 位置づけと代替関係

| 従来ツール | ruff での対応 |
|-----------|---------------|
| Flake8 + プラグイン（bugbear 等） | `ruff check`（ルールを Rust で再実装） |
| Black | `ruff format` |
| isort | lint ルール `I`（`ruff check --select I --fix`） |
| pydocstyle / pyupgrade / autoflake | ルール `D` / `UP` / `F401` 等 |

- 組み込みルールは 900 以上（公式ドキュメント）．
- formatter は Black 整形済みのコードでは出力が変わらないことを目標とする．未整形コードでは意図的な差異がある（例: 末尾コメント付き文の扱い，f-string 内の式の整形，ブロック先頭の空行の削除など）．

### 設定

`pyproject.toml`，`ruff.toml`，`.ruff.toml` のいずれかに書く．同一ディレクトリでは `.ruff.toml` > `ruff.toml` > `pyproject.toml` の優先順で，ファイルごとに最も近い設定ファイルが使われる．設定ファイル間のマージはされず，継承には `extend` を使う．

```toml
[tool.ruff]
line-length = 88
target-version = "py311"

[tool.ruff.lint]
select = ["E", "F", "B", "I", "UP"]   # 例．用途に応じて選ぶ
ignore = ["E501"]

[tool.ruff.lint.per-file-ignores]
"__init__.py" = ["E402"]

[tool.ruff.format]
quote-style = "double"
indent-style = "space"
```

基本コマンド: `ruff check [--fix]`，`ruff format`（CI では `--check`）．

### 連携

- pre-commit: `astral-sh/ruff-pre-commit` の `ruff-check` と `ruff-format` フック．`--fix` を使う場合は lint フックを formatter より前に置く．
- GitHub Actions: `astral-sh/ruff-action`．
- エディタ: ネイティブ言語サーバ（`ruff server`）を使う．旧 `ruff-lsp` は 0.17.0 で削除された．

### 最新動向（2026-11-09 時点）

- 0.15.0: 新しい 2026 年版スタイルガイド，range suppression 対応など（検索結果経由．一次情報は未読）．
- 0.16.0（2026-07-23）: 既定で有効なルールが 59 から 413 に大幅増加（E/F の一部 18 ルールは既定から外れた）．Markdown ファイル内の Python コードブロックを既定で整形．行末に置ける `ruff: ignore` コメント．`check` / `format --check` の出力に fix を表示し，`format --check` が `github` / `gitlab` 等の出力形式に対応．
- 0.17.0（2026-10-09）: 既定の Python バージョンが 3.10 から 3.11 に変更．DTZ の一部ルールが既定から外れ，F406 が既定で有効に．ruff-lsp のサポート削除．macOS / Windows の実行ファイルがコード署名された．
- 最新リリースは 0.17.4（2026-11-05）．
- 2026-03-19，Astral は OpenAI の Codex チームに参加する（OpenAI が Astral を買収する）契約を結んだと発表した．uv / Ruff / ty はオープンソースとして維持するとしている．取引完了の状況は未確認．

## ポイント

- 既定ルール集合はバージョンで変わる（0.16.0 で大幅拡大）．バージョンを固定し，`select` を明示して挙動を安定させる．
- `target-version` / `requires-python` 未指定時の既定が 0.17.0 で py311 になった．
- import ソートは `I` ルール，整形は `ruff format`，と役割が分かれる．`check --fix` の後に `format` を実行する．
- 速度が高く，pre-commit や CI の負担が小さい．
- unsafe fix は明示的に許可しない限り適用されない．

## 関連項目

- [[Python]]
- [[uv]]
- [[ty]]
- [[pytest]]
- [[Rust製CLIツール]]
- [[Rust]]
- [[TOML]]

## 参考

- [Ruff 公式ドキュメント](https://docs.astral.sh/ruff/)
- [Configuring Ruff](https://docs.astral.sh/ruff/configuration/)
- [Black との差異](https://docs.astral.sh/ruff/formatter/black/)
- [Integrations](https://docs.astral.sh/ruff/integrations/)
- [Ruff CHANGELOG](https://github.com/astral-sh/ruff/blob/main/CHANGELOG.md)
- [Ruff 0.16.x changelog](https://github.com/astral-sh/ruff/blob/main/changelogs/0.16.x.md)
- [Ruff releases](https://github.com/astral-sh/ruff/releases)
- [Astral to join OpenAI（Astral 公式ブログ）](https://astral.sh/blog/openai)
