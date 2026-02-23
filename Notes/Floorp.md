---
title: "Floorp"
date: 2026-02-23
tags:
  - Tool
  - Web
related: []
---

## 概要

Floorp は日本の学生チーム「Ablaze」が開発するオープンソースの Web ブラウザ。Firefox をベースとしながら、高度な UI カスタマイズ・垂直タブ・パネルサイドバー・ワークスペースなど Firefox 本体にない機能を追加し、プライバシー保護を重視したブラウザとして注目されている。

## 詳細

### 基本情報

| 項目 | 内容 |
|------|------|
| 開発元 | Ablaze（日本の学生チーム） |
| ベース | Mozilla Firefox |
| ライセンス | Mozilla Public License 2.0 |
| 公式サイト | floorp.app |
| GitHub | github.com/Floorp-Projects/Floorp |
| 対応 OS | Windows、macOS、Linux |
| 最新安定版 | v12.x（2025年〜） |

### バージョン履歴

| バージョン | リリース | 変更点 |
|-----------|---------|--------|
| v11（Floorp 11） | 2023〜2024年 | Firefox ESR ベース、基本機能確立 |
| v12（Floorp 12） | 2025年〜 | Rapid Release ベースに切り替え、コードベース刷新 |
| v12.1.0 | 2025年8月 | 標準 Firefox Rapid Release に戻す |

### 主要機能

#### パネルサイドバー

Floorp の核心機能。サイドバーに Web コンテンツ・ブックマーク・拡張機能などを表示できる。

- **フローティングモード**: サイドバーをウィンドウのように浮かせて自由に配置
- **ホバー表示**: マウスオーバー時のみ表示するオートハイドモード
- **拡張機能統合**: 多くの Firefox 拡張機能をサイドバーパネルとして使用可能
- **メインブラウザへのリンク開放**: サイドバーのリンクをメインビューで開く

#### ワークスペース

タブグループとコンテナを組み合わせた高度なタブ管理。

- ワークスペースごとにタブセット・コンテナを分離（ログイン情報が混在しない）
- Floorp 12 からウィンドウ間でワークスペースが同期
- パネルサイドバーからワークスペースの切り替え・管理

#### 高度な UI カスタマイズ

- **垂直タブ**: タブバーを縦に並べる（画面幅の有効活用）
- **タイトルバーへのツールバー移動**: スペースを節約するレイアウト
- **URL バーとタブバーを同一行に配置**
- **カスタム CSS**: Firefox の `userChrome.css` サポート
- **ブックマークラベルの非表示・切り替え**

#### プライバシー機能

- Firefox の Tracking Protection を継承
- Floorp 12 でサイドバーアイコン取得に使用していた外部 API（Google/DuckDuckGo）を廃止し、内蔵アイコンを使用（外部サーバーへの情報送信を削減）
- Firefox Sync 対応（デバイス間でタブ・ブックマーク共有）

#### その他の機能

| 機能 | 説明 |
|------|------|
| マウスジェスチャー | スワイプでタブ操作・ナビゲーション |
| Floorp Notes | ブラウザ内蔵のメモ機能 |
| PWA サポート | プログレッシブ Web アプリの起動 |
| QR コード生成 | 現在のページ URL を QR コードで表示 |
| カスタムショートカット | キーボードショートカットの変更 |
| Firefox 拡張機能 | 100% 互換（すべての Firefox アドオンが使用可能） |

### Firefox との比較

| 機能 | Firefox | Floorp |
|------|---------|--------|
| 垂直タブ | Firefox 133 から実験的導入 | 以前から安定サポート |
| サイドバー | 基本的な機能のみ | パネルサイドバー（高機能） |
| ワークスペース | コンテナ機能のみ | タブグループ+コンテナ統合 |
| カスタム CSS | 手動設定 | より使いやすいUI |
| 拡張機能互換性 | ◎ | ◎（同等） |

### Chrome/Edge との比較

Floorp は Chromium ベースではなく Firefox ベースのため：
- Google 依存度が低い
- Gecko レンダリングエンジン（多様なブラウザエンジンの維持に貢献）
- Chrome 拡張機能は使用不可（Firefox 拡張機能を使用）

## ポイント

- 日本の学生チームが開発するオープンソース Firefox 派生ブラウザ
- パネルサイドバー・垂直タブ・ワークスペースで Firefox を大幅に機能拡張
- Firefox の全拡張機能と互換性を保ちながら高度な UI カスタマイズが可能
- Floorp 12 でコードベースを刷新、Rapid Release ベースに切り替えて最新機能を素早く取り込む
- プライバシー重視：外部 API への依存を減らす方向で開発

## 関連項目

（現在リンク先の既存ノートなし）

## 参考

- [Floorp 公式サイト](https://floorp.app/)
- [GitHub - Floorp-Projects/Floorp](https://github.com/Floorp-Projects/Floorp)
- [Floorp Browser Blog](https://blog.floorp.app/)
- [Floorp v12.1.0 Release Notes](https://blog.floorp.app/en/release/12.1.0/)
- [Floorp: Highly Customizable Firefox Based Browser - Trishtech](https://www.trishtech.com/2026/02/floorp-highly-customizable-firefox-based-browser/)
