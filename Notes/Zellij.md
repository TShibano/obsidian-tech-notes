---
title: "Zellij"
date: 2026-02-06
tags:
  - Tool
  - CLI
related:
  - "[[Rust]]"
  - "[[Alacritty]]"
  - "[[tmux]]"
  - "[[WebAssembly]]"
---

## 概要

Zellij は Rust で書かれた次世代ターミナルマルチプレクサ。「シンプルさとパワーを両立する」設計思想のもと、直感的な UI、コンテキストアウェアなキーバインド表示、宣言的なレイアウトシステム、WebAssembly ベースのプラグインシステムを備える。

## 詳細

### 基本情報

- **言語**: [[Rust]]
- **設定形式**: KDL（Kdl Document Language）
- **ライセンス**: MIT
- **対応OS**: Linux, macOS
- **アーキテクチャ**: クライアント・サーバー型（Unix ソケット通信）

### 設計思想

Zellij は「シンプルさを犠牲にせずにパワーを提供する」ことを目指している。初心者にも優しいデフォルト設定と、パワーユーザー向けの高度なカスタマイズ性を両立。tmux のような暗記必須の複雑なキーバインドではなく、画面下部にコンテキストに応じたキーバインドを表示する。

### コア機能

#### ペイン・タブ管理

- 水平・垂直分割
- フローティングペイン
- スタックペイン
- タブによるワークスペース管理

#### セッション永続化

ターミナルを誤って閉じてもセッションが保持される。後から再接続可能。

#### レイアウトシステム

KDL ファイルで宣言的にレイアウトを定義。1コマンドでエディタ起動、Docker コンテナ起動、ログ監視、テスト実行などの複雑な環境を再現できる。

```kdl
layout {
    pane split_direction="vertical" {
        pane command="nvim"
        pane split_direction="horizontal" {
            pane command="cargo" args=["watch", "-x", "test"]
            pane command="tail" args=["-f", "app.log"]
        }
    }
}
```

#### プラグインシステム

[[WebAssembly]] ベースのプラグインシステム。任意の言語で開発可能。

| プラグイン例 | 機能 |
|-------------|------|
| Status Bar | CPU、メモリ、バッテリー表示 |
| File Picker | ファイルブラウズ・オープン |
| Tab Manager | タブナビゲーション強化 |

#### その他の機能

- **マウスサポート**: クリック、ドラッグでペイン操作
- **Web クライアント**: ブラウザからターミナルにアクセス
- **マルチプレイヤー**: 複数ユーザーでのリアルタイム共同作業

### 設定

設定ディレクトリ: `~/.config/zellij/`

```bash
# 現在の設定をダンプ
zellij setup --dump-config > ~/.config/zellij/config.kdl
```

### tmux との比較

| 観点 | Zellij | tmux |
|------|--------|------|
| 学習曲線 | 緩やか（UI にキーバインド表示） | 急（暗記必須） |
| 設定形式 | KDL（直感的） | 独自形式（複雑） |
| レイアウト | ファーストクラス機能 | 外部ツール併用 |
| プラグイン | WebAssembly（モダン） | シェルスクリプトベース |
| 安定性 | 比較的新しい | 実績豊富 |
| 互換性 | Linux, macOS | 広範囲 |

Zellij には `tmux` モードがあり、tmux のキーバインドで操作することも可能。

### ユースケース

- **開発環境の統一**: レイアウトファイルで開発環境を宣言的に管理
- **ペアプログラミング**: マルチプレイヤー機能でリアルタイム共同作業
- **サーバー管理**: セッション永続化でリモート作業を安全に
- **tmux からの移行**: tmux モードで段階的に移行

## ポイント

- Rust 製、モダンなターミナルマルチプレクサ
- コンテキストアウェアなキーバインド表示で学習コストが低い
- KDL 形式のレイアウトファイルで宣言的な環境構築
- WebAssembly ベースのプラグインシステム
- tmux モードで既存ユーザーも移行しやすい

## 関連項目

- [[Rust]] - Zellij の実装言語
- [[Alacritty]] - Zellij と組み合わせて使うターミナルエミュレータ
- [[tmux]] - 従来のターミナルマルチプレクサ
- [[WebAssembly]] - プラグインシステムの基盤技術

## 参考

- [Zellij](https://zellij.dev/)
- [GitHub - zellij-org/zellij](https://github.com/zellij-org/zellij)
- [About Zellij - Power Without Complexity](https://zellij.dev/about/)
- [Zellij vs Tmux: Complete Comparison](https://rrmartins.medium.com/zellij-vs-tmux-complete-comparison-or-almost-8e5b57d234ae)
