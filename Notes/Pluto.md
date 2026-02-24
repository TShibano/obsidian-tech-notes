---
title: "Pluto"
date: 2026-02-24
tags:
  - Tool
  - Language
related:
  - "[[Julia]]"
  - "[[Jupyter]]"
  - "[[Marimo]]"
---

## 概要

Pluto.jl は Julia 専用のリアクティブノートブック環境。セルが有向非巡回グラフ（DAG）として管理され、あるセルを変更すると依存するすべてのセルが自動的に再実行される。Jupyter の「隠れ状態問題」を Julia の静的解析能力を活かして根本的に解決し、再現性の高いインタラクティブなサイエンティフィックコンピューティング環境を提供する。

## 詳細

### 基本情報

| 項目 | 内容 |
|------|------|
| 開発元 | Fons van der Plas（JuliaPluto org） |
| ライセンス | MIT |
| GitHub | JuliaPluto/Pluto.jl |
| 公式サイト | plutojl.org |
| インストール | `julia> ] add Pluto` |

### リアクティブ実行モデル

Pluto の最大の特徴は **依存関係グラフによる自動再実行**：

```julia
# セル 1
x = 10       # x が変わると...

# セル 2
y = x * 2    # → 自動的に再実行される

# セル 3
println("y = $y")  # → 自動的に再実行される
```

Julia の静的構文解析でセル間の変数依存関係を自動的に検出し、DAG を構築する。セルを任意の順序で配置しても、Pluto が正しい実行順序を決定する。

### 起動と基本操作

```julia
using Pluto
Pluto.run()
# ブラウザが自動で開き、ノートブックUIが表示される
```

### インタラクティブ UI：@bind マクロ

`@bind` マクロで HTML ウィジェットを Julia 変数に紐付けられる：

```julia
using PlutoUI

# スライダー
@bind x Slider(1:100, default=50)

# チェックボックス
@bind use_log CheckBox()

# セレクトボックス
@bind method Select(["kmeans", "dbscan", "hierarchical"])

# 数値入力
@bind n NumberField(1:1000, default=100)
```

バインドした変数が変化するたびに依存するセルが自動再実行される。

### パッケージ管理の自動化

Pluto はノートブック内の `using` 宣言を自動解析し、必要なパッケージを自動インストール・管理する：

```julia
using Plots        # Pluto が自動で Pkg.add("Plots") を実行
using DataFrames   # バージョンはノートブックごとに固定

# 別のノートブックとは独立した環境（Pkg.toml が内包）
```

この仕組みにより、ノートブック単体で完全な再現性が保証される（他ユーザーが開くだけで同じ環境が再現される）。

### Jupyter との比較

| 観点 | Pluto | Jupyter（Julia/IJulia） |
|------|-------|------------------------|
| 対応言語 | Julia のみ | Julia を含む多言語 |
| リアクティブ実行 | あり（自動） | なし（手動） |
| 隠れ状態 | なし | あり |
| セル実行順序 | 依存関係自動解決 | 上から順 |
| パッケージ管理 | 自動（ノートブック内蔵） | 手動 |
| Git 管理 | `.jl` 形式（読みやすい） | `.ipynb`（JSON・読みにくい） |
| UI ウィジェット | @bind マクロで簡単 | IJulia/Interact.jl |

### ユースケース

- 科学技術計算のインタラクティブな探索
- パラメータを変えながらシミュレーションを可視化
- 教育用コンテンツ（MIT・MIT 6.S083 等で採用）
- 数値解析・微分方程式の可視化
- 講義・チュートリアルスライドの作成

### Marimo との関係

[[Marimo]]（Python）と Pluto（Julia）は同じ「リアクティブノートブック」のコンセプトを共有する：

| 観点 | Pluto | Marimo |
|------|-------|--------|
| 言語 | Julia | Python |
| ファイル形式 | `.jl` | `.py` |
| 実行環境 | Julia REPL | Python インタープリタ |
| 開発年 | 2019年〜 | 2023年〜（Pluto に影響を受けた） |

## ポイント

- Julia の静的解析能力を活かしたリアクティブ実行は、Python ではなく Julia ならではの設計
- パッケージ環境をノートブック内に内包するため、配布・再現が非常に簡単
- `@bind` + PlutoUI で複雑なインタラクティブ UI を数行で作成できる
- MIT 等の大学で教育目的でも広く使われており、学術コンテンツとの相性が良い

## 関連項目

- [[Julia]] - Pluto が動作する言語
- [[Jupyter]] - Python データサイエンスのデファクト標準ノートブック
- [[Marimo]] - Python 版のリアクティブノートブック（Pluto から着想を得た設計）

## 参考

- [Pluto.jl 公式サイト](https://plutojl.org/)
- [GitHub - JuliaPluto/Pluto.jl](https://github.com/JuliaPluto/Pluto.jl)
- [PlutoUI.jl ドキュメント](https://plutojl.org/en/docs/ui/)
