---
title: "Electron"
date: 2026-05-03
tags:
  - Web
  - Framework
  - Tool
related:
  - "[[JavaScript]]"
  - "[[TypeScript]]"
  - "[[Tauri]]"
---

## 概要

Electron は Chromium と Node.js を組み合わせて、JavaScript/HTML/CSS でクロスプラットフォームのデスクトップアプリケーションを構築するフレームワーク。GitHub が 2013 年に開発し、OpenJS Foundation の管理下にある。VSCode・Slack・Discord・Figma など多数の主要デスクトップアプリが Electron で構築されている。

## 詳細

### アーキテクチャ

Electron はマルチプロセス構成を採用する。

```
メインプロセス (Node.js)
  ├─ ウィンドウ管理
  ├─ OS API アクセス（ファイル、通知など）
  └─ アプリライフサイクル管理
        ↕ IPC (ipcMain / ipcRenderer)
レンダラープロセス (Chromium)
  ├─ HTML/CSS/JS による UI
  └─ 各ウィンドウごとに独立したプロセス
```

- **メインプロセス**: Node.js で動作。アプリ全体の制御・OS リソースへのアクセスを担う
- **レンダラープロセス**: Chromium で UI を表示。各ウィンドウが独立したプロセスで動作
- **IPC 通信**: `ipcMain` / `ipcRenderer` API でプロセス間通信

### 主要な採用事例

| アプリ | 開発元 |
|--------|--------|
| Visual Studio Code | Microsoft |
| Slack | Salesforce |
| Discord | Discord Inc. |
| Figma Desktop | Figma |
| 1Password | AgileBits |

### 開発ツール

- **Electron Forge**: ビルド・パッケージング・配布までをカバーするオールインワンツールキット
- **Electron Builder**: クロスプラットフォームビルドと自動更新に特化
- **DevTools**: Chromium の開発者ツールをそのまま利用可能

### Tauri との比較

| 項目 | Electron | [[Tauri]] |
|------|----------|-------|
| バンドルサイズ | ~100MB 以上 | ~600KB〜数 MB |
| メモリ使用量 | 数百 MB | ~30〜40 MB |
| バックエンド言語 | JavaScript (Node.js) | Rust |
| レンダラ | Chromium 同梱 | OS ネイティブ WebView |
| モバイル対応 | △ 非公式 | ○ (v2.0〜) |
| 学習コスト | 低（JS 知識のみ） | 中（Rust 知識が必要な場合も） |
| エコシステム成熟度 | 高（10年以上） | 成長中 |

### セキュリティ

レンダラープロセスからのネイティブ API アクセスは `contextBridge` を通じてメインプロセス経由に制限する設計が推奨される。Node.js Integration をレンダラーで有効化するのはセキュリティリスクがあるため、最新の Electron は `nodeIntegration: false` がデフォルト。

## ポイント

- Web 技術（JS/HTML/CSS）でデスクトップアプリを構築できるため、フロントエンド開発者が学習コストなく参入できる
- Chromium を同梱するためバンドルサイズが大きく（100MB+）、メモリ消費も多い
- 軽量な代替として [[Tauri]]（Rust バックエンド）が台頭しているが、Electron のエコシステム成熟度は依然高い
- Electron Forge でビルド・配布・自動アップデートを一元管理できる
- `contextBridge` + `preload script` によるセキュアな IPC 設計が現在のベストプラクティス

## 関連項目

- [[JavaScript]] — Electron アプリの主要実装言語
- [[TypeScript]] — Electron プロジェクトでよく採用される型安全な代替
- [[Tauri]] — Rust ベースの軽量デスクトップアプリフレームワーク（Electron の代替）

## 参考

- [Electron 公式サイト](https://www.electronjs.org/)
- [Electron ドキュメント](https://www.electronjs.org/docs/latest/)
- [GitHub: electron/electron](https://github.com/electron/electron)
