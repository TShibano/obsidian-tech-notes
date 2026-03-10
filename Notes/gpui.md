---
title: "gpui"
date: 2026-03-10
tags:
  - Language
  - Rust
  - Web
  - Frontend
related:
  - "[[Rust]]"
  - "[[Tauri]]"
  - "[[Leptos]]"
---

## 概要

gpui は Zed Industries が Zed エディタのために開発した、GPU アクセラレーション対応の Rust 製 UI フレームワーク。即時モード（Immediate Mode）と保持モード（Retained Mode）を組み合わせたハイブリッドアーキテクチャにより、120 FPS のレンダリングを実現する。現在は pre-1.0 でアクティブ開発中。

## 詳細

### アーキテクチャ

#### ハイブリッド描画モデル

- **即時モード（Immediate Mode）**: 毎フレーム UI を再構築する。状態管理が単純
- **保持モード（Retained Mode）**: フレーム間で UI の状態をキャッシュする。パフォーマンスに優れる
- gpui はこの両方を組み合わせ、シンプルさと高パフォーマンスを両立

#### 2フェーズ描画パイプライン

1. **prepaint（レイアウト計算）**: Taffy（flexbox レイアウトエンジン）を使って要素の位置・サイズを確定
2. **paint（シーン生成）**: GPU に送るシーングラフを生成

#### プラットフォーム別バックエンド

| OS | ウィンドウシステム | GPU レンダリング |
|----|-----------------|----------------|
| macOS | Cocoa (NSWindow/NSView) | Metal |
| Windows | Win32 API (HWND) | DirectX |
| Linux | X11 / Wayland | Vulkan (Blade) |

### GPU レンダリング戦略

CPU はアプリケーションロジック・イベント処理・レイアウト計算に専念し、ピクセルレンダリングをすべて GPU にオフロード。これにより 120 FPS の滑らかなスクロール・即時キーストローク応答・大きなファイルのカクつきなしの表示を実現している。

ドロップシャドウの描画には Figma 共同創業者の Evan Wallace が開発した手法を採用するなど、ゲームエンジン的なアプローチを取り入れている。

### 開発状況と利用例

- **Zed エディタ**（gpui の主要採用事例）に組み込まれており、継続的に改善されている
- **pre-1.0**：バージョン間で破壊的変更が発生する可能性あり
- 将来的にスタンドアロン crate として独立リリース予定
- コミュニティが gpui を使ったアプリを [awesome-gpui](https://github.com/zed-industries/awesome-gpui) にまとめている

**gpui を使ったアプリ例:**
- Fast Forward（macOS ウィンドウスイッチャー）
- Loungy（アプリランチャー）
- Fulgur（テキスト・コードエディタ）

## ポイント

- GPU を直接活用するため、他の Rust GUI フレームワーク（egui、iced 等）より圧倒的に高いレンダリング性能を目指している
- Taffy による flexbox レイアウトで Web 開発者が直感的に扱えるレイアウトモデルを採用
- pre-1.0 であるため、プロダクション利用よりも実験・コントリビューションの段階
- Zed エディタの OSS 化に伴い、gpui も含めてコミュニティへ公開されている
- Rust の所有権モデルと UI フレームワーク設計の相性の難しさ（エンティティ・コンテキストによる内部状態管理）を独自のパターンで解決している

## 関連項目

- [[Rust]] — 実装言語
- [[Tauri]] — 同じく Rust 製の GUI フレームワーク（Web フロントエンドを組み合わせるアプローチ）
- [[Leptos]] — Rust 製 Web フレームワーク
- [[Helix]] — Rust 製テキストエディタ（gpui は使用していないが比較対象）

## 参考

- [gpui 公式サイト](https://www.gpui.rs/)
- [GitHub: zed-industries/zed (crates/gpui)](https://github.com/zed-industries/zed/tree/main/crates/gpui)
- [Zed Blog: GPU で UI を 120FPS レンダリング](https://zed.dev/blog/videogame)
- [awesome-gpui](https://github.com/zed-industries/awesome-gpui)
