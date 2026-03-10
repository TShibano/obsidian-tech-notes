---
title: "Tauri"
date: 2026-03-10
tags:
  - Language
  - Rust
  - Web
  - Framework
related:
  - "[[Rust]]"
  - "[[Docker]]"
---

## 概要

Tauri は Rust 製のクロスプラットフォーム・デスクトップ／モバイルアプリケーションフレームワーク。フロントエンドを HTML/JS/CSS（任意のフレームワーク）で記述し、バックエンドロジックを Rust で実装する。バンドルサイズが数 MB 以下と極めて小さく、Electron の代替として注目されている。

## 詳細

### アーキテクチャ

Electron と同様のマルチプロセス構成を採用:

- **Core プロセス（Rust）**: アプリケーションロジック、データモデル、システム API アクセスを担う
- **WebView プロセス（JS）**: UI レンダリング。OS ネイティブの WebView（macOS: WKWebView、Windows: WebView2、Linux: WebKitGTK）を利用するため、Chromium を同梱しない

```
フロントエンド (任意のJSフレームワーク)
        ↕ IPC (メッセージパッシング)
Rust バックエンド (Tauri Core)
        ↕
OS ネイティブ API
```

### バージョン履歴

- **Tauri 1.0**: 2022年6月リリース。デスクトップ（Windows/macOS/Linux）対応
- **Tauri 2.0**: 2024年10月リリース。iOS・Android のモバイルサポートを追加。1つのコードベースで5プラットフォームに対応

### Electron との比較

| 項目 | Tauri | Electron |
|------|-------|----------|
| バンドルサイズ | ~600KB〜数 MB | ~100MB 以上 |
| メモリ使用量 | ~30〜40 MB (idle) | 数百 MB |
| 起動速度 | < 500ms | 1〜2秒 |
| バックエンド言語 | Rust | JavaScript (Node.js) |
| レンダラ | OS ネイティブ WebView | Chromium 同梱 |
| モバイル対応 | ○ (v2.0〜) | △ (非公式) |
| セキュリティ | Rust の型安全・メモリ安全 | JavaScript の柔軟性 |

### セキュリティ

Rust のメモリ安全性・型安全性をネイティブに享受できる。メジャーリリース・マイナーリリースごとにセキュリティ監査を実施しており、upstream 依存ライブラリも対象。コマンドの allowlist（許可リスト）により、フロントエンドからのシステムアクセスを最小限に制限できる。

## ポイント

- バンドルサイズが Electron の 1/10〜1/20 程度と極小。配布コストが大幅に下がる
- OS ネイティブ WebView を使うため、レンダリング結果が OS・バージョンにより微妙に異なる点は注意
- Tauri 2.0 から iOS/Android 対応が正式サポート。1コードベースでデスクトップ+モバイルを実現
- Rust の知識が不要でも使えるが、バックエンドをカスタムする場合は Rust が必要
- プラグインエコシステムが整備されており、FS・Shell・HTTP・Notification 等の機能をプラグインとして追加可能

## 関連項目

- [[Rust]] — バックエンドの実装言語
- [[Docker]] — デプロイ・CI パイプラインとの組み合わせ
- [[gpui]] — 同じく Rust 製の GUI フレームワーク（アプローチは異なる）
- [[Leptos]] — Rust 製 Web フレームワーク

## 参考

- [Tauri 2.0 公式サイト](https://v2.tauri.app/)
- [GitHub: tauri-apps/tauri](https://github.com/tauri-apps/tauri)
- [Tauri vs Electron 比較 (DoltHub Blog)](https://www.dolthub.com/blog/2025-11-13-electron-vs-tauri/)
