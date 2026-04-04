---
title: "ratatui"
date: 2026-04-04
tags:
  - Language
  - Rust
  - Tool
  - CLI
related:
  - "[[Rust製CLIツール]]"
  - "[[Zellij]]"
  - "[[Helix]]"
  - "[[Alacritty]]"
---

## 概要

ratatui は、Rust でターミナルユーザーインターフェース（TUI）を構築するための軽量フレームワーク。2023 年に開発停止した tui-rs からフォークされ、コミュニティ主導で活発に開発が継続されている。即時モードレンダリング（Immediate Mode Rendering）を採用し、毎フレームごとに現在の状態から UI を再描画するアプローチをとる。

## 詳細

### 歴史と背景

- **2019年**: Florian Dehau が tui-rs を公開
- **2022年8月**: tui-rs の開発停止に伴い、将来についてのコミュニティ議論が開始
- **2023年2月**: tui-rs からフォークして ratatui が誕生。組織名も tui-rs-revival から ratatui-org へ変更
- **2025年12月**: v0.30.0 リリース（過去最大のリリース）
- GitHub スター数 19.4k 以上、crates.io ダウンロード数 2,200 万以上、3,100 以上のクレートが ratatui を利用

### アーキテクチャ

ratatui は **即時モードレンダリング** を採用している。リテインドモード（React のような状態管理型）とは対照的に、毎フレーム現在のアプリケーション状態から UI 全体を再構築する。

```
┌─────────────────────────────────────┐
│  Application Loop (開発者が管理)      │
│                                     │
│  1. イベント取得（キー入力等）         │
│  2. 状態更新（Model の変更）          │
│  3. UI 描画（View 関数でウィジェット構築）│
│  4. Terminal へレンダリング            │
└─────────────────────────────────────┘
```

- **フレームワークではなくライブラリ**: アプリケーションループの制御権は開発者側にあり、ratatui はウィジェットとレイアウトエンジンを提供するのみ
- **バックエンド抽象化**: crossterm（デフォルト）、termion、termwiz から選択可能
- **View 関数は純粋関数**: 同じ状態からは常に同じ UI が生成される

### アプリケーションパターン

ratatui 自体は特定のアーキテクチャを強制しないが、以下のパターンが一般的:

#### The Elm Architecture（TEA）

- **Model**: アプリケーションの全状態を保持する構造体
- **Update**: イベント（ユーザー入力等）を受け取り、Model を更新
- **View**: Model からターミナル UI 要素への変換（純粋関数）

サードパーティの `ratatui-elm` クレートで Elm アーキテクチャを明示的に実装することも可能。

### 主要ウィジェット

| ウィジェット | 説明 |
|------------|------|
| `Paragraph` | テキスト表示（折り返し・スクロール対応） |
| `List` | スクロール可能なリスト |
| `Table` | テーブル表示（ヘッダー・行選択対応） |
| `Chart` | 折れ線グラフ・散布図（Braille 描画対応） |
| `BarChart` | 棒グラフ |
| `Gauge` | プログレスバー |
| `Sparkline` | スパークライン（小型グラフ） |
| `Canvas` | 自由描画キャンバス |
| `Tabs` | タブ切り替え |
| `Block` | ボーダー・タイトル付きコンテナ |

### レイアウトシステム

制約ベース（Constraint-based）のレスポンシブレイアウトを提供し、ターミナルサイズの変更に自動適応する:

```rust
let chunks = Layout::default()
    .direction(Direction::Vertical)
    .constraints([
        Constraint::Length(3),      // 固定高さ
        Constraint::Min(0),         // 残りスペースを埋める
        Constraint::Percentage(20), // 割合指定
    ])
    .split(area);
```

### v0.30.0 の主要機能（2025年12月）

- **no_std サポート**: 標準ライブラリなしで動作可能。Cortex-M マイクロコントローラ等の組み込み環境で TUI を実行可能に
- **モジュール化**: ワークスペースの再構成によりコンパイル時間・API 安定性・依存管理が改善
- **`ratatui::run()` API**: 合理的なデフォルト設定でターミナルを初期化するシンプルな API
- **crossterm バージョン柔軟性**: `crossterm_0_28` / `crossterm_0_29` フィーチャーフラグでバージョン衝突を回避
- **Chart の Braille over Blocks**: ブロックシンボルの上に Braille チャートを重ねて描画可能

### 代表的な ratatui 製アプリケーション

| アプリ | 説明 |
|--------|------|
| [[Zellij]] | ターミナルマルチプレクサ |
| bottom（btm） | システムモニター |
| gitui | Git の TUI クライアント |
| spotify-tui | Spotify の TUI クライアント |
| kdash | Kubernetes ダッシュボード |

### 他フレームワークとの比較

| 特徴 | ratatui (Rust) | Bubbletea (Go) |
|------|---------------|----------------|
| レンダリング | 即時モード | Elm Architecture |
| ループ制御 | 開発者 | フレームワーク |
| ウィジェット | 同梱 | Bubbles（別パッケージ） |
| パフォーマンス | サブミリ秒レンダリング | 十分高速 |
| 学習曲線 | Rust の所有権を理解する必要あり | Go の簡潔さが有利 |

## ポイント

- tui-rs のコミュニティフォークとして 2023 年に誕生し、Rust TUI エコシステムの事実上の標準となった
- 即時モードレンダリングにより、状態管理がシンプルで予測可能な UI を構築できる
- ライブラリとして設計されており、アプリケーションアーキテクチャの選択は開発者に委ねられている
- v0.30.0 で no_std サポートが追加され、組み込み環境（車のダッシュボード等）でも動作可能に
- 3,100 以上のクレートが依存しており、Rust TUI エコシステムの中核をなす

## 関連項目

- [[Rust製CLIツール]] - Rust で書かれた CLI ツール群の概要
- [[Zellij]] - ratatui を使用したターミナルマルチプレクサ
- [[Helix]] - Rust 製モーダルテキストエディタ
- [[Alacritty]] - Rust 製ターミナルエミュレータ

## 参考

- [ratatui 公式サイト](https://ratatui.rs/)
- [ratatui GitHub リポジトリ](https://github.com/ratatui/ratatui)
- [From tui-rs to Ratatui: 6 Months of Cooking Up Rust TUIs - Orhun's Blog](https://blog.orhun.dev/ratatui-0-23-0/)
- [v0.30.0 ハイライト](https://ratatui.rs/highlights/v030/)
- [The Elm Architecture (TEA) | Ratatui](https://ratatui.rs/concepts/application-patterns/the-elm-architecture/)
- [awesome-ratatui - TUI アプリ・ライブラリ一覧](https://github.com/ratatui/awesome-ratatui)
- [Rendering | Ratatui](https://ratatui.rs/concepts/rendering/)
