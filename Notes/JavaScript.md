---
title: "JavaScript"
date: 2026-03-10
tags:
  - Language
  - Web
  - Frontend
  - Backend
related:
  - "[[TypeScript]]"
  - "[[React]]"
  - "[[Next.js]]"
  - "[[Nuxt]]"
  - "[[Vite]]"
---

## 概要

JavaScript（JS）は 1995 年に Netscape が開発した動的型付けのスクリプト言語。当初はブラウザ上の簡単なインタラクション用途だったが、Node.js の登場によりサーバーサイドにも適用範囲が広がり、フロントエンド・バックエンド・デスクトップ・モバイルまで幅広く使用される現代の汎用言語となった。標準仕様は ECMAScript（TC39 委員会が管理）。

## 詳細

### ECMAScript の進化

年次リリース体制（ES2015 以降）により毎年新機能が追加される。

#### ES2025（ECMAScript 2025）の主要機能

2025年6月25日、Ecma 国際総会で正式承認：

| 機能 | 説明 |
|------|------|
| **Iterator Helpers** | `map()`・`filter()`・`take()`・`drop()` 等のメソッドをイテレータに追加。遅延評価で大きなデータに効率的 |
| **Set 操作メソッド** | `intersection()`・`union()`・`difference()` など集合演算を組み込みサポート |
| **JSON Module Imports** | `import data from './data.json' with { type: 'json' }` でモジュールとして直接インポート |
| **Promise.try()** | 同期・非同期両方の関数を統一的に Promise チェーンに組み込む |
| **RegExp.escape()** | 正規表現特殊文字をエスケープするユーティリティ |
| **Float16Array** | 16ビット浮動小数点数の型付き配列（ML・GPU 用途） |

### JavaScriptの特徴

- **プロトタイプベース OOP**: クラス構文（ES2015〜）は内部的にプロトタイプチェーン
- **イベントループ**: シングルスレッドの非同期実行モデル（コールバック→Promise→async/await）
- **動的型付け**: 実行時に型が決まる。型安全性が必要なら [[TypeScript]] を使用
- **ユビキタス**: ブラウザ・Node.js・Deno・Bun・組み込み環境で動作

### 実行環境

| 環境 | 用途 |
|------|------|
| ブラウザ（V8/SpiderMonkey/JSC） | フロントエンド UI、Web API 操作 |
| Node.js | サーバーサイド、CLI ツール |
| Deno | セキュアな TypeScript ファースト環境 |
| Bun | 超高速 JS ランタイム・バンドラー |

## ポイント

- ES2025 の Iterator Helpers により関数型プログラミングスタイルが標準化
- 型安全性が必要な場合は [[TypeScript]] へ移行が定番
- フレームワークは [[React]]・[[Vue]]・Svelte 等が主流
- バンドラーは [[Vite]] が現在の標準的な選択肢
- WebAssembly（Wasm）により Rust などの言語と組み合わせることが増えている

## 関連項目

- [[TypeScript]] — JavaScript に静的型付けを追加した言語
- [[React]] — JavaScript 製 UI ライブラリ
- [[Next.js]] — React ベースのフルスタックフレームワーク
- [[Nuxt]] — Vue.js ベースのフルスタックフレームワーク
- [[Vite]] — JavaScript フロントエンドの次世代ビルドツール

## 参考

- [ECMAScript 2025 仕様（TC39）](https://tc39.es/ecma262/2025/)
- [ECMAScript 2025 新機能まとめ (2ality)](https://2ality.com/2025/06/ecmascript-2025.html)
- [MDN Web Docs: JavaScript](https://developer.mozilla.org/docs/Web/JavaScript)
