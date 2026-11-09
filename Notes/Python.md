---
title: "Python"
date: 2026-11-09
tags:
  - Language
  - Python
related:
  - "[[uv]]"
  - "[[ruff]]"
  - "[[pytest]]"
  - "[[ty]]"
  - "[[Rust]]"
  - "[[TypeScript]]"
  - "[[Jupyter]]"
  - "[[Docker]]"
---

## 概要

Python は Guido van Rossum が 1989 年末に実装を始めた汎用の動的型付けスクリプト言語で，可読性の高い構文と大規模な標準ライブラリ（batteries included）を特徴とする．AI / ML，データ分析，Web バックエンド，自動化など幅広い分野の事実上の標準言語であり，2026-10-09 に Python 3.15 がリリースされた．

## 詳細

### 歴史と設計思想

- 1989 年のクリスマス休暇ごろ，オランダ CWI の Amoeba 分散 OS グループにいた Guido van Rossum が実装を開始した．システム管理用に，C や Bourne shell より良い道具が必要だったことが動機
- 先行言語 ABC の影響（インデントによる文のグループ化，高水準データ型）を受け，ABC に欠けていた拡張性を重視した．例外処理は Modula-3 などの影響を受けている
- 1991 年 2 月に USENET へ公開．名前は *Monty Python's Flying Circus* に由来する
- 設計原則: 明快な構文，C / C++ による拡張性，手続き型・オブジェクト指向・関数型の複数パラダイム，大きな標準ライブラリ
- 設計の美学は PEP 20 "The Zen of Python"（`import this`）にまとめられている（"Readability counts" など）

### バージョンとリリーススケジュール（2026-11-09 時点）

| バージョン | 状態 | 初回リリース | サポート終了（予定） |
|-----------|------|--------------|----------------------|
| 3.15 | bugfix | 2026-10-09 | 2031-10 |
| 3.14 | bugfix | 2025-10-07 | 2030-10 |
| 3.13 | security | 2024-10-07 | 2029-10 |
| 3.10 | end-of-life | 2021-10-04 | 2026-10-01 |

- 現行の安定版は 3.15（3.15.2 のドキュメントが公開済み）．年 1 回，10 月にメジャー（機能）リリースが出る
- 各バージョンは約 2 年のバグ修正（約 2 か月ごとのリリース）と，その後 5 年目までのセキュリティ修正（ソースのみ）を受ける
- 3.14 の最終バグ修正リリースは 3.14.16（2027-10-05 予定）．3.14.10 は 2026-10-26 にリリース済み
- 日付は予定であり調整されうる（イタリック表記）．3.10 は 2026-10 に EOL を迎えたため，新規プロジェクトは 3.13 以降を選ぶ

### free-threaded（GIL なし）ビルド

PEP 703 によって GIL を無効化したビルドが導入され，PEP 779 で段階的に正式化されている．

| フェーズ | 内容 | 時期 |
|----------|------|------|
| Phase I | 実験的ビルドとして提供 | 3.13 |
| Phase II | 正式サポート（ただしオプション） | 3.14 |
| Phase III | デフォルト化 | 未定（将来の PEP で判断） |

- 3.13: 別実行ファイル（`python3.13t`）として実験的に提供．`PYTHON_GIL` / `-X gil=1` で実行時に GIL を有効化可能．`sys._is_gil_enabled()` で状態確認できる
- 3.14: PEP 779 により正式サポート．適応型インタプリタ（PEP 659）が free-threaded でも有効になり，単一スレッド性能の低下は約 5〜10%
- 3.15: Stable ABI 向けの `abi3t`（PEP 803）が追加され，C 拡張を free-threaded に対応させやすくなった．公式 macOS バイナリは free-threading サポートをデフォルトで同梱する
- GIL を使わない C 拡張を読み込んでいない場合のみ GIL なしで動く（非対応拡張を import すると GIL が有効化される）ため，エコシステムの対応状況が採用の鍵

### JIT コンパイラ

- 3.13: PEP 744 の実験的 JIT（コピー・アンド・パッチ方式）が追加．`--enable-experimental-jit` でビルドし，デフォルトは無効，性能向上は控えめ
- 3.14: Windows / macOS の公式バイナリが実験的 JIT に対応．また tail-call インタプリタ（Clang 19 以降，x86-64 / AArch64，オプトイン）が追加され，pyperformance で幾何平均 3〜5% 高速との暫定結果
- 3.15: JIT が大きく改良され，pyperformance の幾何平均で x86-64 Linux では通常インタプリタ比 7〜8%，AArch64 macOS では tail-call インタプリタ比 11〜12% 高速．JIT なしのビルドと比べるとベンチマークにより約 15% 低下から 100% 超の高速化まで幅がある．依然として「実験的」位置づけ

### 近年の主な言語機能

- 3.13: 新しい対話シェル（PyPy 由来，複数行編集・色付き表示）
- 3.14: テンプレート文字列 t-string（PEP 750），アノテーションの遅延評価（PEP 649 / 749，`annotationlib`），`concurrent.interpreters`（PEP 734，サブインタプリタ），`compression.zstd`（PEP 784）
- 3.15: 明示的な遅延インポート `lazy import`（PEP 810，モジュールスコープのみ），UTF-8 をデフォルトエンコーディングに（PEP 686，`PYTHONUTF8=0` で旧挙動），`typing.TypeForm`（PEP 747），`@typing.disjoint_base`（PEP 800），`profile` モジュールの非推奨化（3.17 で削除予定，`profiling.tracing` へ移行）

### ツールチェーンとの関係

近年のツールチェーンは Rust 製の高速ツールが主流になりつつある．

| ツール | 役割 | 備考 |
|--------|------|------|
| [[uv]] | パッケージ / プロジェクト / Python 本体のバージョン管理 | Astral 製．pip・venv・pyenv 等を置き換える |
| [[ruff]] | Linter / Formatter | Astral 製．flake8・Black・isort 等を置き換える |
| [[ty]] | 型チェッカー / 言語サーバー | Astral 製，Rust 実装．公式ドキュメントは mypy / Pyright より 10〜100 倍高速と主張．PyPI の分類は Beta（0.0.x 系） |
| [[pytest]] | テストフレームワーク | 最新は 9.2.0（PyPI．Python 3.10 以上が必要） |

- 2026-03-19 に Astral は OpenAI の Codex チームに参加する契約を発表した．uv / ruff / ty は引き続きオープンソースとして維持されると表明されているが，コミュニティには懸念の声もある（クローズ状況は未確認）
- ty は Rust 実装でエディタ統合（LSP）も備え，`uvx ty check` で手軽に実行できる

## ポイント

- 最新は 3.15（2026-10-09）．3.10 は 2026-10-01 に EOL，3.13 以降を使う
- リリースは年 1 回（10 月），バグ修正約 2 年 + セキュリティ修正で計 5 年サポート
- free-threaded は 3.14 で正式サポート（オプション）に昇格したが，デフォルト化は未定．C 拡張の対応が課題
- JIT は 3.15 で改善したが依然実験的．効果はワークロード依存で，退化する場合もある
- ツールチェーンは uv / ruff / ty（Astral）+ pytest が現代的な標準構成．Astral は 2026-03 に OpenAI への参加を発表している
- 3.15 では UTF-8 デフォルト化など，互換性に影響する変更がある

## 関連項目

- [[uv]]
- [[ruff]]
- [[ty]]
- [[pytest]]
- [[Rust]]
- [[TypeScript]]
- [[Jupyter]]
- [[Docker]]

## 参考

- [Python 3.14 Release Schedule (PEP 745)](https://peps.python.org/pep-0745/)
- [Status of Python versions (Python Developer's Guide)](https://devguide.python.org/versions/)
- [What's New In Python 3.15](https://docs.python.org/3.15/whatsnew/3.15.html)
- [What's New In Python 3.14](https://docs.python.org/3/whatsnew/3.14.html)
- [What's New In Python 3.13](https://docs.python.org/3/whatsnew/3.13.html)
- [PEP 779 – Criteria for supported status for free-threaded Python](https://peps.python.org/pep-0779/)
- [General Python FAQ](https://docs.python.org/3/faq/general.html)
- [ty documentation](https://docs.astral.sh/ty/)
- [Astral: Astral to join OpenAI](https://astral.sh/blog/openai)
- [pytest on PyPI](https://pypi.org/project/pytest/)
