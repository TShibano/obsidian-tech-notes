---
title: "Vite"
date: 2026-03-10
tags:
  - Web
  - Frontend
  - Tool
related:
  - "[[JavaScript]]"
  - "[[TypeScript]]"
  - "[[React]]"
  - "[[Nuxt]]"
  - "[[Leptos]]"
  - "[[Trunk]]"
---

## 概要

Vite（フランス語で「速い」）は Evan You（Vue.js 作者）が開発したフロントエンドビルドツール。開発時は ES モジュール（ESM）のネイティブサポートを活用してバンドル不要の高速サーバー起動を実現し、本番ビルドは Rollup でバンドルする。React・Vue・Svelte・Solid など主要フレームワークをサポートし、現代の JavaScript 開発の標準的な選択肢となっている。

## 詳細

### なぜ Vite が速いのか

#### 開発時：ESM ベースのサーブ

従来のバンドラー（Webpack 等）はすべてのモジュールをバンドルしてから開発サーバーを起動するため、大規模プロジェクトでは起動に数十秒かかる。

Vite のアプローチ：
1. **アプリのコードをバンドルしない**: ブラウザが `import` を解釈した時点でファイルを個別リクエスト
2. **オンデマンドトランスフォーム**: リクエストされたファイルのみを変換して返す
3. **esbuild での依存関係プリバンドル**: `node_modules` の依存を Go 製 esbuild で高速に変換

```
ブラウザが import を解析
    → Vite にリクエスト
    → Vite が該当ファイルのみ変換
    → ブラウザに返す
```

#### HMR（Hot Module Replacement）の最適化

変更されたモジュールとそれに直接依存するモジュールだけを無効化。ページ全体を再読み込みしない。

### 主な機能

| 機能 | 説明 |
|------|------|
| **TypeScript サポート** | `.ts`/`.tsx` をネイティブサポート（型チェックなし高速トランスパイル） |
| **CSS Modules / Sass** | CSS モジュール・Sass/SCSS を設定なしで使用可能 |
| **静的アセット処理** | 画像・フォントのインポートに対応 |
| **環境変数** | `.env` ファイルをサポート、`import.meta.env` でアクセス |
| **プラグイン** | Rollup 互換のプラグイン API |

### Vite のエコシステム（2025〜）

- **Vite+ / VoidZero**: 2025年10月の ViteConf で発表。Vite を CLI ツールチェーン全体に拡張するプロジェクト。Evan You が率いる VoidZero 社が開発
- **Vitest**: Vite ネイティブのユニットテストフレームワーク
- **Nuxt**: Vue.js のメタフレームワークが Vite をデフォルトバンドラーとして採用
- **SvelteKit / Remix**: Vite ベースに移行済み

### vite.config.ts の基本

```ts
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  build: {
    outDir: 'dist',
    rollupOptions: {
      output: {
        manualChunks: { vendor: ['react', 'react-dom'] }
      }
    }
  }
});
```

## ポイント

- 開発時は ESM のネイティブサポートによりバンドル不要 → 起動がほぼ瞬時
- esbuild（Go 製）で依存関係をプリバンドルすることで高速化
- React・Vue・Svelte 等すべての主要フレームワークで使用可能
- TypeScript・CSS Modules・Sass をゼロ設定でサポート
- [[Next.js]] は独自に Turbopack（Rust 製）を採用しているため Vite とは別ルート
- [[Leptos]]・[[Trunk]] などの Rust/Wasm エコシステムからも影響を受けつつある

## 関連項目

- [[JavaScript]] — Vite がビルドする主要言語
- [[TypeScript]] — Vite がネイティブサポートする言語
- [[React]] — Vite で最も多く使われるフレームワーク
- [[Nuxt]] — Vite をデフォルトバンドラーとして採用した Vue フレームワーク
- [[Leptos]] — Rust 製フロントエンドフレームワーク（Wasm ビルドは [[Trunk]] を使用）

## 参考

- [Vite 公式サイト](https://vite.dev)
- [Vite 公式ドキュメント: Getting Started](https://vite.dev/guide/)
- [GitHub: vitejs/vite](https://github.com/vitejs/vite)
