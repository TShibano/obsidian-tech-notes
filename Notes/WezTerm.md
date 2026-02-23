---
title: "WezTerm"
date: 2026-02-23
tags:
  - Tool
  - CLI
related:
  - "[[Rust]]"
  - "[[Alacritty]]"
  - "[[Ghostty]]"
  - "[[Warp]]"
  - "[[Zellij]]"
---

## 概要

WezTerm（Wez's Terminal Emulator）は Wez Furlong が Rust で開発した GPU アクセラレーション対応のクロスプラットフォームターミナルエミュレータ兼マルチプレクサ。ターミナル本体にタブ・ペイン分割・SSH マルチプレクサ機能を内蔵し、Lua スクリプトで高度なカスタマイズが可能。

## 詳細

### 基本情報

| 項目 | 内容 |
|------|------|
| 開発者 | Wez Furlong |
| 言語 | [[Rust]] |
| レンダリング | OpenGL（GPU アクセラレーション） |
| ライセンス | MIT |
| 対応 OS | Windows、macOS、Linux、BSD |
| 設定形式 | Lua（ホットリロード対応） |

### 主要機能

#### 内蔵マルチプレクサ

WezTerm は単体でタブ・ペイン分割・ウィンドウ管理を提供する。外部マルチプレクサ（[[Zellij]] や tmux）を必要とせずに複数のターミナルセッションを管理できる。

**ワークスペース機能**: 複数のウィンドウ・タブ・ペインをワークスペース（名前付きセッション）にグループ化し、プロジェクト・クライアント業務・サーバー管理などを切り替えながら作業できる。

#### SSH マルチプレクサ

WezTerm のマルチプレクサプロトコルは SSH 経由でも動作する。リモートホストでもローカルと同じタブ・ペイン操作が可能で、SSH セッションの接続断後も状態を保持できる。

#### Lua 設定

設定ファイル `~/.wezterm.lua`（または `~/.config/wezterm/wezterm.lua`）を Lua で記述。設定変更はホットリロードされ、WezTerm を再起動せずに即時反映される。

```lua
-- ~/.wezterm.lua の例
local wezterm = require 'wezterm'
local config = {}
config.font = wezterm.font('JetBrains Mono')
config.font_size = 14.0
config.color_scheme = 'Tokyo Night'
return config
```

#### テキスト操作・表示機能

- リガチャ（合字）、カラー絵文字、フォントフォールバック対応
- iTerm2 画像プロトコル対応（ターミナル内画像表示）
- Quick Select（マウスなしでテキストをコピー）
- コピーモード（キーボードでスクロールバックバッファを操作）
- クリック可能なハイパーリンク

#### シリアルポート・Arduino 対応

SSH だけでなくシリアルポート接続もサポートしており、組み込み開発（Arduino 等）にも活用できる。

### 設定例

```lua
-- ペイン分割ショートカット設定
local action = wezterm.action
config.keys = {
  { key = 'd', mods = 'CTRL|SHIFT', action = action.SplitHorizontal {} },
  { key = 's', mods = 'CTRL|SHIFT', action = action.SplitVertical {} },
}
```

### 他ターミナルとの比較

| ターミナル | マルチプレクサ内蔵 | 設定言語 | SSH マルチプレクサ |
|-----------|-----------------|---------|----------------|
| **WezTerm** | あり | Lua | あり |
| [[Ghostty]] | あり | 独自形式 | なし |
| [[Alacritty]] | なし | TOML | なし |
| [[Zellij]] | — （マルチプレクサ専用） | KDL | SSH 接続は手動 |

## ポイント

- Rust 製、GPU アクセラレーションで高パフォーマンス
- タブ・ペイン・ワークスペースを単体で提供（外部マルチプレクサ不要）
- SSH マルチプレクサにより、リモート環境もローカル同様に操作
- Lua 設定のホットリロードで設定変更を即時反映
- クロスプラットフォーム（Windows、macOS、Linux、BSD）

## 関連項目

- [[Rust]] - WezTerm の実装言語
- [[Alacritty]] - Rust 製、シンプル志向のターミナル
- [[Ghostty]] - Zig 製、ネイティブ UI 重視のターミナル
- [[Warp]] - AI 統合ターミナル
- [[Zellij]] - Rust 製ターミナルマルチプレクサ

## 参考

- [WezTerm 公式サイト](https://wezterm.org/)
- [WezTerm Features](https://wezterm.org/features.html)
- [WezTerm Configuration](https://wezterm.org/config/files.html)
- [GitHub - wezterm/wezterm](https://github.com/wezterm/wezterm)
