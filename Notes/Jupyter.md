---
title: "Jupyter"
date: 2026-02-24
tags:
  - Tool
  - AI
  - Language
related:
  - "[[Marimo]]"
  - "[[Pluto]]"
  - "[[RAG]]"
---

## 概要

Jupyter（Project Jupyter）はデータサイエンス・機械学習・科学技術計算のデファクトスタンダードのノートブック環境。コード・数式・可視化・テキストを一つの文書（`.ipynb`）に統合し、ブラウザ上でインタラクティブに実行できる。Jupyter Notebook（クラシック版）と JupyterLab（次世代版）の2つの主要インターフェースが存在する。

## 詳細

### 基本情報

| 項目 | 内容 |
|------|------|
| 開発元 | Project Jupyter（非営利） |
| 元の名称 | IPython Notebook（2014年以前） |
| 名称の由来 | Julia・Python・R の頭文字 |
| ライセンス | BSD |
| 公式サイト | jupyter.org |
| ファイル形式 | `.ipynb`（JSON ベース） |

### Jupyter Notebook vs JupyterLab

| 観点 | Jupyter Notebook（クラシック） | JupyterLab（次世代） |
|------|------------------------------|---------------------|
| リリース | 2011年 | 2018年（GA）、v4.0: 2023年 |
| UI | シンプル・単一ペイン | タブ・マルチペイン・IDE ライク |
| 拡張性 | 拡張機能あり | 豊富なプラグインエコシステム |
| ターミナル | 別画面 | 統合ターミナル |
| テキストエディタ | 別アプリ | 内蔵エディタ |
| ファイルブラウザ | 簡素 | 統合ファイルブラウザ |
| 推奨 | 簡単な利用 | 本格的な開発 |

**現在は JupyterLab がメインの開発ターゲット**（Jupyter Notebook もサポート継続中）。

### インストールと起動

```bash
# pip でインストール
pip install jupyterlab

# 起動
jupyter lab

# Notebook（クラシック版）
jupyter notebook
```

### ノートブックの基本構造

`.ipynb` ファイルは JSON 形式で以下のセルタイプを持つ：

| セルタイプ | 用途 |
|-----------|------|
| Code | コードの記述・実行（出力を保存） |
| Markdown | テキスト・数式（LaTeX）・画像 |
| Raw | メタデータ・変換用素材 |

### 主要なカーネル

Jupyter は「カーネル」として任意の言語をサポートする：

| カーネル | 言語 |
|---------|------|
| IPython | Python（デフォルト） |
| IJulia | Julia |
| IRkernel | R |
| IHaskell | Haskell |
| その他 | C++・Go・Ruby・Scala など50言語以上 |

### JupyterHub

チーム・組織向けのマルチユーザー Jupyter サーバー。クラウド環境や大学の計算クラスタで広く利用される：

```bash
# JupyterHub インストール
pip install jupyterhub
jupyterhub
```

### 主要な拡張・統合

| ツール | 説明 |
|--------|------|
| JupyterHub | マルチユーザー対応 |
| nbconvert | `.ipynb` → HTML/PDF/スクリプト変換 |
| nbformat | ノートブックファイル形式ライブラリ |
| Voilà | ノートブックをインタラクティブ Web アプリとして配信 |
| papermill | ノートブックのパラメータ化・バッチ実行 |
| jupytext | `.ipynb` ↔ `.py/.md` の相互変換（Git 管理向け） |

### Jupyter の課題

Jupyter の「隠れ状態問題」は有名な設計上の課題：

```
セル 1: x = 10
セル 3: x = 20     ← セル 2 の前に実行
セル 2: print(x)   ← 20 が表示される（混乱の原因）
```

セルを非線形に実行すると、変数の状態が見た目と異なる可能性がある。この問題を解決する次世代ノートブックとして [[Marimo]]（Python）・[[Pluto]]（Julia）が開発されている。

## ポイント

- データサイエンス・ML の事実上の標準環境として圧倒的な普及度とエコシステムを持つ
- Kaggle・Google Colab・Amazon SageMaker など主要プラットフォームが対応
- 実行順序に依存した「隠れ状態問題」は再現性の観点で課題（[[Marimo]] が解決策）
- JupyterLab はマルチペイン・拡張機能・ターミナル統合で IDE に近い体験を提供
- nbconvert・papermill によりパイプライン組み込み・レポート自動生成も可能

## 関連項目

- [[Marimo]] - Jupyter の次世代代替（リアクティブ・Pure Python）
- [[Pluto]] - Julia 向けリアクティブノートブック
- [[RAG]] - Jupyter 上での LLM/RAG 実装実験

## 参考

- [Project Jupyter 公式サイト](https://jupyter.org/)
- [JupyterLab ドキュメント](https://jupyterlab.readthedocs.io/)
- [JupyterHub ドキュメント](https://jupyterhub.readthedocs.io/)
- [What is Jupyter Notebook? Why It's essential for AI and data science - Nebius](https://nebius.com/blog/posts/what-is-jupyter-notebook-for-ai)
