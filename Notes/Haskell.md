---
title: "Haskell"
date: 2026-05-03
tags:
  - Language
related:
  - "[[関数型プログラミング]]"
  - "[[Erlang]]"
  - "[[Gleam]]"
---

## 概要

Haskell は 1987 年に学術コミュニティが設計した純粋関数型プログラミング言語．静的型付け・型推論・遅延評価を特徴とし，プログラムの正確性を数学的に保証することを重視する．コンパイラは GHC（Glasgow Haskell Compiler）が事実上の標準で，金融・暗号・コンパイラ開発などの高信頼性が求められる領域で採用されている．

## 詳細

### 言語の特徴

| 特徴 | 説明 |
|------|------|
| 純粋関数型 | すべての関数が副作用なし．副作用は `IO` モナドで明示的に扱う |
| 静的型付け + 型推論 | 強力な型システムを持ちつつ，多くの場合型注釈が不要 |
| 遅延評価 | デフォルトで式の評価を必要になるまで遅らせる |
| 代数的データ型 | `data Maybe a = Nothing \| Just a` のような豊富な型表現 |
| 型クラス | Java のインターフェースに相当する型の振る舞いの抽象化 |
| モナド | `IO`・`Maybe`・`Either`・`State` 等で副作用・エラー・状態を型安全に扱う |

### 型システムと型クラス

Haskell の型システムは Hindley-Milner 型推論を拡張した高度なもの．

```haskell
-- 型クラスの定義
class Eq a where
  (==) :: a -> a -> Bool

-- 代数的データ型
data Shape = Circle Double | Rectangle Double Double

-- パターンマッチ
area :: Shape -> Double
area (Circle r)        = pi * r * r
area (Rectangle w h)   = w * h

-- Maybe モナドによる安全な計算
safeDivide :: Double -> Double -> Maybe Double
safeDivide _ 0 = Nothing
safeDivide x y = Just (x / y)
```

### モナドと IO

Haskell は副作用を `IO` モナドに隔離することで，純粋な関数と副作用のある処理を型レベルで分離する．

```haskell
main :: IO ()
main = do
  putStrLn "名前を入力してください:"
  name <- getLine
  putStrLn ("こんにちは，" ++ name ++ "さん！")
```

### ツールチェーン

| ツール | 説明 |
|--------|------|
| **GHC** | Glasgow Haskell Compiler．事実上の標準コンパイラ |
| **Cabal** | パッケージマネージャー兼ビルドシステム．v3.14 が現行 |
| **Stack** | プロジェクト管理ツール．Stackage スナップショットで依存を固定 |
| **HLS** | Haskell Language Server．IDE 連携用 LSP サーバー |
| **GHCup** | GHC・HLS・Cabal・Stack を管理するツールチェーン管理ツール |

### 2026 年の動向

- GHC 9.12 + Cabal 3.14 が現行の安定バージョン
- Haskell Language Server の安定性・パフォーマンス改善が進行中
- ICFP 2026（インディアナポリス）でコミュニティが集結予定
- 金融・暗号・コンパイラ・型理論研究での採用が継続

### 主な採用領域

- **金融システム**: Standard Chartered，Jane Street（OCaml とともに）
- **コンパイラ・ツール**: GHC 自体が Haskell で実装，Pandoc（文書変換）
- **暗号・セキュリティ**: 高い正確性が要求される領域
- **学術研究**: 型理論・圏論・プログラム検証

## ポイント

- 「副作用は `IO` モナドに隔離」という設計により，純粋な関数と副作用のある処理が型で明確に区別される
- 型クラス・代数的データ型・パターンマッチが揃った強力な型システムで，コンパイルが通ればほぼ正しい動作を保証できる
- 遅延評価により無限リストなどを自然に扱えるが，メモリ使用量の予測が難しい場合もある
- 学習曲線は急峻だが，モナド・型クラス等の概念は他言語（Rust の `Option`/`Result`，Scala 等）にも応用できる
- 実用ツールとして Pandoc・Xmonad・Hledger などが Haskell で実装されている

## 関連項目

- [[関数型プログラミング]] — Haskell が体現する純粋関数型パラダイム
- [[Erlang]] — 同じく関数型で並行処理に特化した言語
- [[Gleam]] — Haskell に似た型システムを持ち BEAM 上で動作する言語

## 参考

- [Haskell 公式サイト](https://www.haskell.org/)
- [Haskell - Wikipedia](https://en.wikipedia.org/wiki/Haskell)
- [Learn You a Haskell for Great Good!](https://learnyouahaskell.com/)
- [GHCup — Haskell ツールチェーン管理](https://www.haskell.org/ghcup/)
