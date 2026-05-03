---
title: "Gleam"
date: 2026-05-03
tags:
  - Language
related:
  - "[[関数型プログラミング]]"
  - "[[Erlang]]"
  - "[[Elixir]]"
  - "[[BeamVM]]"
---

## 概要

Gleam は静的型付けの関数型プログラミング言語で、Erlang の BEAM VM と JavaScript の両方にコンパイルできる。Louis Pilfold が 2016 年に開発を開始し、Erlang/Elixir エコシステムの型安全な代替として注目されている。モダンで親しみやすい構文と強力な型システムを持ち、2025 年の Stack Overflow Developer Survey では「最も愛されている言語」第2位を獲得した。

## 詳細

### 特徴

Gleam は Erlang の BEAM VM 上で動作するため、Erlang の高い並行性・耐障害性をそのまま享受できる。同時に JavaScript にもコンパイルでき、フロントエンドにも活用可能。

| 特徴 | 説明 |
|------|------|
| 静的型付け | コンパイル時に型エラーを検出。型消去なし |
| 関数型 | 純粋関数・イミュータブルデータ中心 |
| BEAM 上で動作 | [[Erlang]] / [[Elixir]] のライブラリを利用可能 |
| JS コンパイル | ブラウザ・Node.js にもデプロイ可能 |
| 親しみやすい構文 | Rust/TypeScript に近いモダンな構文 |

### BEAM エコシステムとの相互運用

Gleam は Erlang・Elixir のパッケージを直接利用できる。Hex（BEAM の公式パッケージリポジトリ）上の数千のパッケージが使用可能。既存の Erlang/Elixir システムへの段階的な導入も容易。

```gleam
import gleam/io
import gleam/list

pub fn main() {
  let numbers = [1, 2, 3, 4, 5]
  let doubled = list.map(numbers, fn(x) { x * 2 })
  io.println(doubled |> list.to_string)
}
```

### 型システム

Haskell に似た代数的データ型（ADT）とパターンマッチを採用。`Result(value, error)` 型でエラーハンドリングを型安全に表現する。`null` や例外は存在せず、エラーは明示的に型で表現される。

```gleam
pub type Result(value, error) {
  Ok(value)
  Error(error)
}

fn divide(a: Float, b: Float) -> Result(Float, String) {
  case b {
    0.0 -> Error("ゼロ除算")
    _   -> Ok(a /. b)
  }
}
```

### ツールチェーン

- **gleam build / run / test**: 標準的なコマンドでビルド・実行・テスト
- **Hex パッケージマネージャー**: Erlang/Elixir 共通のパッケージリポジトリ
- **LSP サポート**: 主要なエディタ（VS Code, Helix 等）でコード補完・型確認が可能

## ポイント

- BEAM の並行処理・耐障害性 + 静的型付けの安全性を両立
- Erlang/Elixir の資産をそのまま活用できるため既存エコシステムへの参入コストが低い
- `null` や例外を排除し、`Result` 型で安全なエラーハンドリングを強制
- JavaScript コンパイル対応によりフルスタック開発が1言語で可能
- 2025 年 Stack Overflow 調査で「最も愛されている言語」2位を獲得した新興言語

## 関連項目

- [[関数型プログラミング]] — Gleam が採用するプログラミングパラダイム
- [[BeamVM]] — Gleam がコンパイルする仮想マシンの詳細
- [[Erlang]] — Gleam がコンパイルするターゲット VM（BEAM）の元祖言語
- [[Elixir]] — 同じ BEAM 上で動作する代表的な言語

## 参考

- [Gleam 公式サイト](https://gleam.run/)
- [Gleam - Wikipedia](https://en.wikipedia.org/wiki/Gleam_(programming_language))
- [Introduction to Gleam - The New Stack](https://thenewstack.io/introduction-to-gleam-a-new-functional-programming-language/)
