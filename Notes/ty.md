---
title: "ty"
date: 2026-11-09
tags:
  - Language
  - Python
  - Tool
  - CLI
related:
  - "[[Python]]"
  - "[[uv]]"
  - "[[ruff]]"
  - "[[pytest]]"
---

## 概要

ty は Astral が開発する，Rust 製の Python 型チェッカー兼言語サーバー．mypy / Pyright / Pylance の代替を狙い，高速性と偽陽性の少なさを重視する．2025-12-16 に beta となったが，2026-11 時点で 1.0（stable）は未到達で，バージョンは 0.0.x が続く．

## 詳細

### 位置づけと開発ステータス

- 2025-12-16 に初の Beta をリリース．「motivated users」には本番利用も推奨する段階とされた．Astral は翌年（2026 年）の Stable を目標にしている
- 2026-11-09 時点の最新は 0.0.99（2026-11-06）．0.0.x のため，バージョン間で診断を含む破壊的変更が起こりうる
- 2026-03-19，Astral は OpenAI の Codex チームに参加する（OpenAI が Astral を買収する）契約を結んだと発表した．uv / Ruff / ty はオープンソースとして維持するとしている．取引完了の状況は未確認（ty と Ruff のライセンスは MIT）
- 設計方針: 言語サーバーを前提にした増分計算アーキテクチャ，第一級の intersection 型，高度な型の絞り込み（narrowing），ユーザー意図の推測による偽陽性の抑制

### 最近のリリース（2026-10〜11）

| バージョン | 日付 | 主な変更 |
|---|---|---|
| 0.0.92 | 2026-10-26 | `--output-format sarif`，Django モデルフィールドの型推論 |
| 0.0.93 | 2026-10-28 | diagnostic baseline をプレビュー機能として導入 |
| 0.0.97 | 2026-11-04 | `unresolved-import` を `import-not-found` に改名 |
| 0.0.98 | 2026-11-05 | 改名済みルール使用時の警告，PEP 723 スクリプトがプロジェクト設定を継承 |
| 0.0.99 | 2026-11-06 | LSP `workspace/willRenameFiles` 対応 |

ルール名の改名があるため，設定ファイルの `[tool.ty.rules]` は更新時に確認が必要．

### インストールと使い方

```bash
uvx ty check                 # インストール不要で実行
uv add --dev ty && uv run ty check   # プロジェクトに追加
uv tool install ty@latest    # グローバル
pip install ty / pipx install ty
```

- 既定では作業ディレクトリ／プロジェクト内の Python ファイルをすべて検査する
- サブコマンドは `check`，`server`，`explain` など
- ブラウザで試せる playground: play.ty.dev
- エディタ: VS Code 拡張が公式に提供され，LSP 対応エディタ（PyCharm，Neovim 等）で動作する．定義ジャンプ，リネーム，補完，自動インポート，inlay hint に対応

### 設定

- `pyproject.toml` の `[tool.ty]` か `ty.toml` に記述（同一ディレクトリに両方あれば `ty.toml` が優先）
- ユーザー設定は `~/.config/ty/ty.toml`．プロジェクト設定が優先され，配列はマージされる．CLI オプションが最優先

```toml
[tool.ty.rules]
index-out-of-bounds = "ignore"
```

### mypy / Pyright / Pyrefly との比較

| 観点 | mypy | Pyright | Pyrefly (Meta) | ty |
|---|---|---|---|---|
| 実装 | Python | TypeScript | Rust | Rust |
| 成熟度 | 成熟（2.0: 2026-05-06） | 成熟 | 1.0（2026-05-12）以降 | beta（0.0.x） |
| 未注釈関数の本体 | 既定でスキップ | standard で検査 | basic ではスキップ | 検査する |
| 未注釈関数の戻り値 | `Any` | 推論 | basic: `Unknown`，default: 推論 | 既定 `Unknown`（`return-type-inference = "always"` で推論） |
| strict プリセット | `--strict` | `strict` | `preset = "strict"` | なし（ルール単位で設定） |
| 設定移行支援 | - | - | mypy 設定の変換あり | 手動 |

- 速度: Astral は，キャッシュなしで mypy / Pyright より 10〜60 倍高速，編集後の再計算は Pyright の 80 倍と主張（ベンダー主張）．公式ドキュメントのトップでは「10x - 100x」と記載
- 型仕様への適合度（typing conformance suite）: 出典ごとに数値が異なるが，順序は Pyright > Pyrefly > ty / mypy で，ty は最も低い側とする二次情報が多い．ty は適合度より推論の実用性を優先する方針
- ty と Pyrefly は検出するバグの種類が異なるとする分析があり，どちらが優れるかは用途次第．mypy 風のプラグイン機構は ty にも Pyrefly にもない
- 二次情報では，ほとんどのプロジェクトの既定には Pyrefly が推奨され，uv / Ruff を使うプロジェクトで ty の推論ポリシーが合う場合に ty が向くとされる

## ポイント

- Astral の Rust 製ツール群（[[uv]]，[[ruff]]）に続く型チェッカーで，LSP を第一に設計されている
- beta・0.0.x のため，バージョンを固定し（dev 依存に追加），更新時はルール改名などの変更を確認する
- 既存の mypy 環境からは自動移行ツールがなく，mypy プラグイン依存（Django，SQLAlchemy 等）のプロジェクトは CI で mypy を併用するのが無難
- 未注釈コードも検査するため，段階的導入（gradual adoption）しやすい

## 関連項目

- [[Python]]
- [[uv]]
- [[ruff]]
- [[pytest]]

## 参考

- [ty: the next generation of Python type checking (Astral blog)](https://astral.sh/blog/ty)
- [ty documentation](https://docs.astral.sh/ty/)
- [ty Installation](https://docs.astral.sh/ty/installation/)
- [ty Configuration](https://docs.astral.sh/ty/configuration/)
- [ty Releases (GitHub)](https://github.com/astral-sh/ty/releases)
- [How do Python type checkers compare? (pydevtools)](https://pydevtools.com/handbook/explanation/how-do-mypy-pyright-and-ty-compare/)
- [ty vs Pyrefly (pydevtools)](https://pydevtools.com/handbook/explanation/ty-vs-pyrefly.md)
- [Astral to join OpenAI（Astral 公式ブログ）](https://astral.sh/blog/openai)
