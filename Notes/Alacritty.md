---
title: "Alacritty"
date: 2026-02-06
tags:
  - Tool
  - CLI
related:
  - "[[Rust]]"
  - "[[Zellij]]"
  - "[[tmux]]"
---

## 概要

Alacritty は Rust で書かれた GPU アクセラレーション対応のクロスプラットフォームターミナルエミュレータ。パフォーマンスとシンプルさに特化しており、「1つのことをうまくやる」Unix 哲学に基づき、タブやペイン分割などの機能は意図的に省略している。

## 詳細

### 基本情報

- **開発開始**: 2017年1月（Joe Wilm による発表）
- **言語**: [[Rust]]
- **レンダリング**: OpenGL（GPU アクセラレーション）
- **ライセンス**: Apache License 2.0
- **最新バージョン**: 0.16.1
- **対応OS**: BSD, Linux, macOS, Windows

### 設計思想

Alacritty は「1つのことをうまくやる」Unix 哲学を採用している。ターミナルエミュレータとしての機能に特化し、タブやペイン分割は提供しない。これらの機能は [[tmux]] や [[Zellij]] などのターミナルマルチプレクサと組み合わせて使用することを想定している。

### コア機能

#### GPU レンダリング

OpenGL を使用した GPU レンダリングにより、大画面でのテキスト描画で約 500 FPS を実現。ビルドログのスクロールやテスト出力の確認時に、滑らかな動作を体感できる。

#### Vi モード

`Ctrl+Shift+Space` で起動。キーボードでビューポートやスクロールバック内を移動できる。検索や URL オープン機能への起点としても機能する。

#### 検索機能

スクロールバックバッファ内を検索可能。前方検索は `Ctrl+Shift+f`（macOS は `Cmd+f`）、後方検索は `Ctrl+Shift+b`（macOS は `Cmd+b`）。

#### ターミナルヒント

正規表現で検出したテキスト要素（URL など）を外部アプリケーションに渡したり、組み込みアクションをトリガーしたりできる。

#### マルチウィンドウ

同一 Alacritty インスタンスから複数のターミナルウィンドウを起動可能。

### 設定

#### 設定ファイル

- **形式**: TOML（v0.13.0 以降）
- **場所**: `~/.config/alacritty/alacritty.toml`（Linux/BSD）、`~/.config/alacritty/alacritty.toml`（macOS）
- **自動リロード**: 設定ファイルの変更は自動的に反映される

#### 主要な設定セクション

| セクション | 内容 |
|-----------|------|
| `[general]` | 一般設定 |
| `[window]` | ウィンドウ設定 |
| `[cursor]` | カーソル設定 |
| `[selection]` | 選択設定 |
| `[keyboard]` | キーバインド |

#### テーマのインポート

```toml
import = [
  "~/.config/alacritty/themes/dracula.toml"
]
```

### 他ツールとの比較

| ターミナル | 特徴 |
|-----------|------|
| **Alacritty** | 最速、シンプル、マルチプレクサ前提 |
| Kitty | GPU、組み込みタブ/分割、画像表示 |
| iTerm2 | macOS 専用、多機能 |
| Warp | AI 統合、モダン UI |

Alacritty と Kitty は GPU アクセラレーションにより、Electron ベースや従来のターミナルより高速。

## ポイント

- Rust 製、GPU アクセラレーションで最速クラスの性能
- タブ・ペイン分割なし（マルチプレクサとの併用前提）
- TOML 形式の設定ファイル、自動リロード対応
- Vi モード、検索、ターミナルヒントなどキーボード操作が充実
- クロスプラットフォーム（BSD, Linux, macOS, Windows）

## 関連項目

- [[Rust]] - Alacritty の実装言語
- [[Zellij]] - Rust 製ターミナルマルチプレクサ
- [[tmux]] - 定番のターミナルマルチプレクサ

## 参考

- [Alacritty - A cross-platform, OpenGL terminal emulator](https://alacritty.org/)
- [GitHub - alacritty/alacritty](https://github.com/alacritty/alacritty)
- [Alacritty - ArchWiki](https://wiki.archlinux.org/title/Alacritty)
- [Alacritty's configuration](https://alacritty.org/config-alacritty.html)
- [Releases - alacritty/alacritty](https://github.com/alacritty/alacritty/releases)
