---
title: "TOML"
date: 2026-02-09
tags:
  - Tool
  - DevOps
related:
  - "[[JSON]]"
  - "[[YAML]]"
  - "[[データフォーマット]]"
  - "[[Rust]]"
---

## 概要

TOML（Tom's Obvious, Minimal Language）は、設定ファイルに特化した読みやすく書きやすいファイルフォーマットである。GitHub 共同創業者の Tom Preston-Werner によって2013年に考案され、[[Rust]] の `Cargo.toml` や Python の `pyproject.toml` など、多くのプロジェクト管理ツールで標準的な設定形式として採用されている。

## 詳細

### 歴史と仕様

TOML は INI ファイル形式の曖昧さを解消し、明確なセマンティクスを持つ設定ファイル形式として設計された。

| バージョン | リリース日 | 備考 |
|-----------|-----------|------|
| v0.1.0 | 2013-03-17 | 初期リリース。Semantic Versioning に準拠 |
| v1.0.0 | 2021-01-12 | 安定版リリース |
| v1.1.0 | 策定中 | 次期バージョン |

設計目標:

- 人間が読みやすい最小限の設定ファイル形式であること
- ハッシュテーブルに曖昧さなくマッピングできること
- 多様なプログラミング言語で容易にパースできること

### 基本構文

```toml
# コメント
title = "TOML Example"

[owner]
name = "Tom Preston-Werner"
dob = 1979-05-27T07:32:00-08:00  # 日時型をネイティブサポート

[database]
enabled = true
ports = [8001, 8001, 8002]
connection_max = 5000

[servers.alpha]
ip = "10.0.0.1"
role = "frontend"

[servers.beta]
ip = "10.0.0.2"
role = "backend"
```

特徴的な構文要素:

- **テーブル（セクション）**: `[table]` で名前空間を定義
- **ドット記法**: `[servers.alpha]` でネストしたテーブルを表現
- **日時型**: RFC 3339 準拠の日時型をネイティブでサポート
- **コメント**: `#` でコメントが記述可能
- **配列テーブル**: `[[array]]` で同一キーの配列を定義

### サポートするデータ型

| データ型 | 例 | 備考 |
|---------|---|------|
| 文字列 | `"hello"`, `'literal'` | 基本文字列とリテラル文字列 |
| 整数 | `42`, `0xFF`, `0o755`, `0b1010` | 各種基数表記に対応 |
| 浮動小数点 | `3.14`, `inf`, `nan` | IEEE 754 準拠 |
| 真偽値 | `true`, `false` | |
| 日時 | `2021-01-12T07:32:00Z` | RFC 3339 準拠。ローカル日時もサポート |
| 配列 | `[1, 2, 3]` | |
| テーブル | `[section]` | |

### 主な用途・採用事例

#### Rust（Cargo.toml）

[[Rust]] のパッケージマネージャー Cargo は、プロジェクトの設定ファイルとして `Cargo.toml` を使用する。依存関係、メタデータ、ビルド設定などを管理する。

#### Python（pyproject.toml）

Python のパッケージングエコシステムは `pyproject.toml` を標準の設定ファイルとして採用し、従来の `setup.py` を置き換えた。Poetry 2.0（2025年1月リリース）も `[project]` テーブルをサポートしている。

#### その他の採用例

- **Hugo**: 静的サイトジェネレーターの設定ファイル
- **Go**: 多くの Go プロジェクトで採用
- **Julia**: プロジェクト設定
- **Blender**: アドオンマニフェスト
- **Gradle**: バージョンカタログ

### TOML vs YAML vs JSON

| 観点 | TOML | [[YAML]] | [[JSON]] |
|------|------|------|------|
| 設計目的 | 設定ファイル | 汎用シリアライゼーション | データ交換 |
| 可読性 | 高い | 高い | 中程度 |
| コメント | 対応 | 対応 | 非対応 |
| 日時型 | ネイティブ対応 | ライブラリ依存 | 非対応 |
| 深いネスト | 苦手 | 得意 | 得意 |
| パース安全性 | 安全 | 要注意 | 安全 |
| 主な用途 | プロジェクト設定 | DevOps / K8s | API / データ交換 |

TOML はフラットから中程度のネスト構造に最適であり、深いネスト構造には向かない。深い階層が必要な場合は [[YAML]] や [[JSON]] の方が適している。

## ポイント

- TOML は設定ファイルに特化した明確で読みやすいフォーマット
- Tom Preston-Werner（GitHub 共同創業者）が2013年に考案し、2021年に v1.0.0 が安定版としてリリース
- ハッシュテーブルへの曖昧さのないマッピングが設計上の特徴
- 日時型のネイティブサポートが [[JSON]] や [[YAML]] にない利点
- [[Rust]] の `Cargo.toml`、Python の `pyproject.toml` など、モダンなパッケージ管理ツールで標準採用
- フラットな構造に最適であり、深いネストが必要な場合は他のフォーマットを検討すべき

## 関連項目

- [[JSON]] - 汎用的なデータ交換フォーマット
- [[YAML]] - DevOps 領域で広く使われるシリアライゼーション言語
- [[データフォーマット]] - データ形式の体系的な整理
- [[Rust]] - Cargo.toml として TOML を標準採用しているプログラミング言語

## 参考

- [TOML 公式サイト v1.0.0](https://toml.io/en/v1.0.0)
- [TOML GitHub リポジトリ](https://github.com/toml-lang/toml)
- [TOML v1.1.0（策定中）](https://toml.io/en/v1.1.0)
- [Python Packaging User Guide - Writing your pyproject.toml](https://packaging.python.org/en/latest/guides/writing-pyproject-toml/)
