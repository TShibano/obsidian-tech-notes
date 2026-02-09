---
title: "Rust"
date: 2026-02-06
tags:
  - Language
  - Rust
related:
  - "[[WebAssembly]]"
  - "[[C++]]"
  - "[[Alacritty]]"
  - "[[Zellij]]"
  - "[[jujutsu]]"
  - "[[Polars]]"
  - "[[TOML]]"
---

## 概要

Rust は Mozilla が開発したシステムプログラミング言語で、メモリ安全性、高速性、並行性を兼ね備える。ガベージコレクタなしでメモリ安全性を保証する所有権システムが最大の特徴であり、2026年現在も開発者から最も称賛される言語として評価されている。

## 詳細

### 歴史と背景

Rust は 2006 年に Mozilla のエンジニア Graydon Hoare が個人プロジェクトとして開始した。エレベーターのソフトウェアがクラッシュしたことへの不満がきっかけだったという。2009 年に Mozilla が公式にスポンサーとなり、2012 年に最初の公開リリース（0.1）、2015 年に安定版 1.0 がリリースされた。

2020 年の Mozilla レイオフ後、2021 年に Rust Foundation が設立され、Mozilla 以外の企業も支援に加わった。

### コア機能

#### 所有権システム

Rust の最も革新的な機能。コンパイル時にメモリの所有権を追跡し、以下を保証する：

- メモリリークの防止
- ダングリングポインタの排除
- データ競合の防止

#### ゼロコスト抽象化

高レベルの抽象化を使用しても、手書きの低レベルコードと同等のパフォーマンスを実現する。

#### 安全な並行処理

`Arc`、`Mutex`、`RwLock` などの同期プリミティブにより、スレッドセーフなコードを書きやすい。コンパイラがデータ競合を検出する。

### 最新動向（2026年）

- **現行バージョン**: Rust 1.93.0（安定版）、1.94.0 Beta（2026年3月5日予定）
- **Rust 2024 Edition**: Rust 1.85.0 で安定化
- **開発者数**: 226万人以上が過去12ヶ月で使用、70万人以上が主要言語として使用
- **評価**: Stack Overflow 調査で83%の称賛率を維持

2026年の主要テーマ：
- **WASM/WASI**: プラットフォーム間のポータビリティ向上
- **安全な FFI 境界**: C/C++ との相互運用性改善
- **autodiff**: 自動微分のフロントエンドがほぼ完成

### 主要なユースケース

| 分野 | 説明 | 採用例 |
|------|------|--------|
| システムプログラミング | OS、デバイスドライバ、組み込みシステム | Redox OS、Linux カーネルモジュール |
| クラウドインフラ | マイクロVM、コンテナランタイム | AWS Firecracker |
| バックエンド開発 | 高性能サーバー、レイテンシ削減 | Discord |
| ネットワーク | プロキシ、ロードバランサ | Cloudflare Pingora |
| CLI ツール | 高速で信頼性の高いコマンドラインツール | ripgrep, exa |

### 採用企業

- **Microsoft**: Windows コアライブラリの書き換え、Azure プロジェクト
- **AWS**: Firecracker（Lambda/Fargate 基盤）
- **Cloudflare**: Pingora（1日1兆リクエスト処理）
- **Meta**: 内部ソースコード管理ツール
- **Shopify**: YJIT（CRuby JIT コンパイラ）

## ポイント

- 所有権システムによりガベージコレクタなしでメモリ安全性を実現
- C/C++ と同等の性能を持ちながら、安全性が高い
- 2026年も開発者から最も称賛される言語（83%）
- Linux カーネル、Windows、主要クラウドインフラで採用が進む
- `unsafe` コードは最小限に抑え、文書化することがベストプラクティス

## 関連項目

- [[WebAssembly]] - Rust の主要なコンパイルターゲット
- [[C++]] - Rust が置き換えを目指す言語
- [[Alacritty]] - Rust 製ターミナルエミュレータ
- [[Zellij]] - Rust 製ターミナルマルチプレクサ
- [[jujutsu]] - Rust 製 Git 互換バージョン管理システム
- [[Polars]] - Rust 製高性能 DataFrame ライブラリ
- [[TOML]] - Cargo.toml として Rust が標準採用する設定ファイルフォーマット

## 参考

- [Rust (programming language) - Wikipedia](https://en.wikipedia.org/wiki/Rust_(programming_language))
- [The Rust Programming Language Blog](https://blog.rust-lang.org/)
- [Rust Versions | Rust Changelogs](https://releases.rs/)
- [Project goals update — December 2025 | Rust Blog](https://blog.rust-lang.org/2026/01/05/project-goals-2025-december-update/)
- [Rust VS C++ Comparison for 2026 | JetBrains](https://blog.jetbrains.com/rust/2025/12/16/rust-vs-cpp-comparison-for-2026/)
