---
title: "cargo-generate"
date: 2026-04-04
tags:
  - Language
  - Rust
  - Tool
  - CLI
related:
  - "[[Rust製CLIツール]]"
  - "[[Docker]]"
---

## 概要

cargo-generate は、既存の Git リポジトリをテンプレートとして活用し、Rust プロジェクトのスキャフォールディングを行う開発者ツール。`cargo new` の拡張版として、プレースホルダー置換・条件分岐・フック実行など、柔軟なテンプレートエンジンを提供する。

## 詳細

### 基本的な使い方

```bash
# GitHub リポジトリをテンプレートとして使用
cargo generate username/mytemplate

# プラットフォーム別ショートカット
cargo generate gh:username/repo   # GitHub
cargo generate gl:username/repo   # GitLab
cargo generate bb:username/repo   # Bitbucket
cargo generate sr:username/repo   # SourceHut

# プロジェクト名を指定
cargo generate --name myproject username/template
```

`cargo gen` という短縮形も使用可能。

### テンプレートエンジン

cargo-generate は **Shopify の Liquid テンプレート言語** を使用し、以下の仕組みでテンプレートを処理する:

#### 組み込みプレースホルダー

| プレースホルダー | 内容 |
|-----------------|------|
| `{{project-name}}` | `--name` フラグまたは対話入力で指定されたプロジェクト名 |
| `{{crate_name}}` | project-name の snake_case 版 |
| `{{authors}}` | Cargo の設定から取得した著者情報 |

#### テンプレート定義プレースホルダー

テンプレート作者が `cargo-generate.toml` でカスタムプレースホルダーを定義できる:

```toml
[placeholders]
# 自由入力
db_name = { prompt = "データベース名は？" }

# 選択肢を制限
framework = { prompt = "使用するフレームワーク", choices = ["actix", "axum", "rocket"] }

# デフォルト値付き
port = { prompt = "ポート番号", default = "8080" }
```

#### Liquid テンプレート構文

`.liquid` 拡張子のファイルは自動的に処理され、拡張子が除去される:

```rust
// main.rs.liquid
fn main() {
    {% if framework == "axum" %}
    // Axum server setup
    {% elsif framework == "actix" %}
    // Actix server setup
    {% endif %}
    println!("Starting {{project-name}} on port {{port}}");
}
```

### 条件分岐と高度な機能

#### ファイルの条件付きインクルード

`cargo-generate.toml` で Rhai スクリプトを使い、ファイルの include/exclude を制御:

```toml
[conditional.'is_binary']
include = ["src/main.rs"]
exclude = ["src/lib.rs"]

[conditional.'!is_binary']
include = ["src/lib.rs"]
exclude = ["src/main.rs"]
```

`--bin` / `--lib` コマンドライン引数でバイナリかライブラリかを選択可能。

#### フックスクリプト

Rhai スクリプトでテンプレート展開前後にカスタム処理を実行できる。`file::exists(path)` メソッドでファイル存在チェックも可能。

### サブテンプレート

一つのリポジトリに複数のテンプレートを格納でき、ユーザーに選択させることが可能。サブテンプレートの表示順序も設定可能で、最初のオプションがデフォルトとなる。サブテンプレート自体は独自の `cargo-generate.toml` を必要としない。

### 代表的なテンプレート

cargo-generate は多くの Rust フレームワーク・エコシステムで採用されている:

| テンプレート | 用途 |
|-------------|------|
| `rustwasm/wasm-pack-template` | WebAssembly プロジェクト（wasm-pack 用） |
| `embassy-rs/embassy` | 組み込み Rust（Embassy フレームワーク） |
| `leptos-rs/start` | Leptos Web フレームワーク |
| `cargo-generate/wasm-pack-template` | 公式 WASM テンプレート |

GitHub の `cargo-generate` トピックでテンプレートを検索可能。

### cargo new との比較

| 機能 | `cargo new` | `cargo-generate` |
|------|-------------|-------------------|
| 基本プロジェクト生成 | ○ | ○ |
| カスタムテンプレート | × | ○ |
| プレースホルダー置換 | × | ○（Liquid） |
| 条件分岐 | × | ○（Rhai） |
| フックスクリプト | × | ○ |
| Git リポジトリから取得 | × | ○ |
| サブテンプレート | × | ○ |

## ポイント

- `cargo new` を超える柔軟なプロジェクトスキャフォールディングを提供する Cargo サブコマンド
- Liquid テンプレート言語と Rhai スクリプトの組み合わせにより、条件分岐・プレースホルダー・フック実行が可能
- wasm-pack、Leptos、Embassy 等の主要 Rust フレームワークが公式テンプレートとして cargo-generate を採用
- テンプレート作者はカスタムプレースホルダーで対話的にプロジェクト構成を決定させることができる
- 他言語における cookiecutter（Python）や Yeoman（JavaScript）に相当するツール

## 関連項目

- [[Rust製CLIツール]] - Rust で書かれた CLI ツール群の概要
- [[Docker]] - Dockerfile のテンプレート化にも応用可能

## 参考

- [cargo-generate GitHub リポジトリ](https://github.com/cargo-generate/cargo-generate)
- [cargo-generate 公式ドキュメント](https://cargo-generate.github.io/cargo-generate/)
- [テンプレート一覧（TEMPLATES.md）](https://github.com/cargo-generate/cargo-generate/blob/main/TEMPLATES.md)
- [テンプレート定義プレースホルダー](https://cargo-generate.github.io/cargo-generate/templates/template_defined_placeholders.html)
- [条件分岐ドキュメント](https://cargo-generate.github.io/cargo-generate/templates/conditional.html)
