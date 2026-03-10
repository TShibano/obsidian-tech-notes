---
title: "Trunk"
date: 2026-03-10
tags:
  - Language
  - Rust
  - Web
  - Frontend
  - Tool
related:
  - "[[Rust]]"
  - "[[Leptos]]"
  - "[[Vite]]"
---

## 概要

Trunk は Rust 製の WebAssembly（Wasm）Web アプリケーションバンドラー。Rust のコードを Wasm にコンパイルし、JavaScript・CSS・画像などのアセットとともにブラウザで実行可能なアプリケーションにまとめる。[[Leptos]]・Yew・Dioxus などの Rust 製フロントエンドフレームワークの標準的なビルドツールとして使用される。JavaScript エコシステムにおける [[Vite]] に相当するポジション。

## 詳細

### 動作原理

Trunk は HTML ファイルをエントリーポイントとして使用する。特別な属性を持つ `<link>` タグを解析し、対応するビルドパイプラインを実行する：

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
  <head>
    <link data-trunk rel="rust" />          <!-- Rust コードを Wasm にコンパイル -->
    <link data-trunk rel="css" href="style.css" />  <!-- CSS バンドル -->
    <link data-trunk rel="icon" href="favicon.ico" />
  </head>
</html>
```

### ビルドパイプライン

```
Trunk build
  1. cargo build --target wasm32-unknown-unknown
  2. wasm-bindgen を実行（Wasm バインディング生成）
  3. wasm-opt で最適化（オプション）
  4. CSS・画像・JS スニペットのアセットパイプライン
  5. 出力ディレクトリ (dist/) にバンドル
```

#### wasm-bindgen との連携

Rust の関数・型を JavaScript から呼び出せるバインディングを自動生成。`#[wasm_bindgen]` アトリビュートで注釈したコードが JavaScript インターフェイスとして公開される。

### 主な機能

| 機能 | 説明 |
|------|------|
| **開発サーバー** | `trunk serve` でローカルサーバーを起動 |
| **ファイルウォッチ** | ソース変更を検知して自動ビルド・ブラウザリロード |
| **HTTP プロキシ** | API サーバーへのプロキシ設定 |
| **WebSocket プロキシ** | WebSocket の転送サポート |
| **アセット処理** | CSS・Sass・画像・フォントのバンドル |
| **Wasm 最適化** | wasm-opt による出力 Wasm のサイズ削減 |

### インストールと基本的な使い方

```bash
# インストール
cargo install trunk

# 開発サーバー起動（ホットリロード付き）
trunk serve

# プロダクションビルド
trunk build --release
```

### 主要な Rust フロントエンドフレームワークとの関係

| フレームワーク | 概要 |
|--------------|------|
| [[Leptos]] | Rust 製フルスタック Web フレームワーク。CSR モードで Trunk を使用 |
| Yew | React ライクな Rust 製フロントエンドフレームワーク |
| Dioxus | Rust 製クロスプラットフォーム UI フレームワーク（Web・デスクトップ・モバイル） |

## ポイント

- Rust/Wasm エコシステムのフロントエンドビルドで事実上の標準ツール
- HTML をエントリーポイントとするシンプルな設定（最小限の設定ファイルで動作）
- `cargo build` → `wasm-bindgen` の一連のパイプラインを自動化
- [[Leptos]] の CSR（クライアントサイドレンダリング）開発に必須
- [[Vite]] と比べると Wasm 特化であり、JavaScript フレームワークとの混在は想定していない

## 関連項目

- [[Rust]] — Trunk がビルドするプログラミング言語
- [[Leptos]] — Trunk と組み合わせて使う Rust 製 Web フレームワーク
- [[Vite]] — JavaScript エコシステムにおける対応ビルドツール

## 参考

- [Trunk 公式サイト](https://trunkrs.dev/)
- [GitHub: trunk-rs/trunk](https://github.com/trunk-rs/trunk)
- [MDN: Rust から WebAssembly へのコンパイル](https://developer.mozilla.org/docs/WebAssembly/Guides/Rust_to_Wasm)
