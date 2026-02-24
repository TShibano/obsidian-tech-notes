---
title: "Marimo"
date: 2026-02-24
tags:
  - Tool
  - AI
  - Language
related:
  - "[[Jupyter]]"
  - "[[Pluto]]"
  - "[[RAG]]"
---

## 概要

Marimo は次世代のリアクティブ Python ノートブック。セルの変更が依存するすべてのセルに自動で伝播する「リアクティブ実行モデル」を採用し、ノートブックをスクリプト・アプリとしても実行可能なピュア Python ファイルとして保存する。Jupyter の隠れ状態問題を根本的に解決し、再現性・Git 親和性・デプロイ容易性を高めた現代的なデータサイエンス環境。

## 詳細

### 基本情報

| 項目 | 内容 |
|------|------|
| 開発元 | marimo-team |
| ライセンス | Apache 2.0 |
| GitHub | marimo-team/marimo（15k+ stars） |
| 公式サイト | marimo.io |
| インストール | `pip install marimo` |
| 月次ダウンロード | 数十万件（2025年時点） |

### リアクティブ実行モデル

Marimo の核心は**データフローグラフ**に基づく実行モデル：

```python
# セル A
import marimo as mo
x = mo.slider(1, 100, value=10)  # インタラクティブ UI

# セル B（セル A に依存）
y = x.value * 2  # x が変わると自動的に再実行

# セル C（セル B に依存）
print(f"y = {y}")  # y が変わると自動的に再実行
```

従来の Jupyter では、セルを手動で再実行しなければ変更が反映されなかった（隠れ状態問題）。Marimo はこれを静的解析により解決する。

### 主要機能

#### Pure Python ファイル形式

```python
# marimo ノートブックは通常の .py ファイル
# スクリプトとして実行も可能
# Git diff が読みやすい

import marimo
app = marimo.App()

@app.cell
def cell_1():
    x = 10
    return x,

@app.cell
def cell_2(x):
    print(x * 2)
    return
```

#### インタラクティブ UI

```python
# スライダー・テーブル・ドロップダウンなど
slider = mo.slider(0, 100)
table = mo.table(df)
dropdown = mo.dropdown(["option1", "option2"])
```

#### SQL サポート

```python
# Python 変数を SQL クエリに埋め込める
result = mo.sql(f"SELECT * FROM my_table WHERE value > {threshold.value}")
```

#### AI アシスタント

コンテキスト認識の AI コード補完（変数メモリの状態を理解した上でコードを提案）。

#### アプリとしてのデプロイ

```bash
# ノートブックをインタラクティブ Web アプリとして起動
marimo run notebook.py

# 通常のスクリプトとして実行
python notebook.py
```

### Jupyter との比較

| 観点 | Marimo | Jupyter |
|------|--------|---------|
| 隠れ状態 | なし（リアクティブ） | あり（手動実行依存） |
| ファイル形式 | Pure Python（.py） | JSON（.ipynb） |
| Git diff | 読みやすい | 読みにくい（JSON） |
| インタラクティブ UI | ネイティブ対応 | ipywidgets（複雑） |
| スクリプト実行 | 標準対応 | jupytext 等が必要 |
| アプリデプロイ | 内蔵 | Voilà 等が必要 |
| 再現性 | 高い | 低い（実行順序依存） |

### ユースケース

- データ分析・EDA（探索的データ解析）
- 機械学習実験の管理と再現
- インタラクティブダッシュボードの作成・共有
- 教育・チュートリアルコンテンツ
- [[RAG]] や LLM を使ったデータ探索スクリプト

## ポイント

- Jupyter の「実行順序依存・隠れ状態」問題をリアクティブモデルで根本解決
- `.py` 形式保存により Git 管理・コードレビューが標準の Python プロジェクトと同じ感覚に
- `marimo run` でノートブックをそのまま Web アプリとして公開可能（Streamlit 的な用途）
- SQL を Python 変数と組み合わせてインラインで書けるため、データ探索が非常に流暢
- Cloudflare・Shopify・BlackRock などの大企業でも採用

## 関連項目

- [[Jupyter]] - Python ノートブックのデファクトスタンダード
- [[Pluto]] - Julia 向けのリアクティブノートブック（同じリアクティブ設計思想）
- [[RAG]] - LLM を活用したデータ探索との組み合わせ

## 参考

- [Marimo 公式サイト](https://marimo.io/)
- [GitHub - marimo-team/marimo](https://github.com/marimo-team/marimo)
- [Marimo ドキュメント](https://docs.marimo.io/)
- [marimo as a Jupyter alternative](https://marimo.io/features/vs-jupyter-alternative)
- [Python notebooks as dataflow graphs](https://marimo.io/blog/dataflow)
