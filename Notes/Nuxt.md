---
title: "Nuxt"
date: 2026-03-10
tags:
  - Web
  - Frontend
  - Backend
  - Framework
related:
  - "[[JavaScript]]"
  - "[[TypeScript]]"
  - "[[Vite]]"
  - "[[Next.js]]"
---

## 概要

Nuxt は Vue.js をベースにしたフルスタック Web フレームワーク（メタフレームワーク）。SSR・SSG・ISR・エッジレンダリングに対応し、Vite をデフォルトのビルドツールとして採用する。React エコシステムにおける [[Next.js]] に相当する立ち位置。2025年7月に Nuxt 4.0 がリリースされ、2026年に向けて Nuxt 5（Nitro v3）の準備が進んでいる。

## 詳細

### Nuxt のアーキテクチャ

```
Nuxt アプリケーション
├── Vue 3（フロントエンドフレームワーク）
├── Vite（開発サーバー・バンドラー）
└── Nitro（サーバーエンジン）
       └── Node.js / サーバーレス / エッジ環境で動作
```

**Nitro** は Nuxt のサーバーエンジン。Node.js・Cloudflare Workers・Vercel Edge・AWS Lambda など複数のデプロイ先をサポートする「ユニバーサルサーバーエンジン」。

### Nuxt 4.0（2025年7月リリース）

#### プロジェクト構造の改善

- `app/` ディレクトリ（旧 `pages/`/`components/`/`layouts/` の上位）を導入し、ルーティング関連ファイルをまとめて管理
- TypeScript サポートの強化
- 高速化された CLI

#### データフェッチングの改善（Nuxt 4.2〜）

- **ネイティブリクエストキャンセル**: フェッチの中断を制御できる abort 機能
- **非同期ハンドラ抽出**: バンドルサイズを最大39%削減できる最適化

```vue
<script setup>
// useFetch・useAsyncData は SSR 対応のデータフェッチ
const { data } = await useFetch('/api/users');
</script>
```

### レンダリングモード

| モード | 説明 |
|--------|------|
| **SSR** | デフォルト。各リクエストをサーバーでレンダリング |
| **SSG** | ビルド時に静的 HTML を生成（`nuxt generate`）|
| **ISR** | ルートルールで再生成タイミングを指定 |
| **SPA** | クライアントのみでレンダリング（`ssr: false`） |
| **エッジSSR** | Nitro 経由で CDN エッジで SSR |

Route Rules により 1 アプリ内で混在設定が可能：

```ts
// nuxt.config.ts
export default defineNuxtConfig({
  routeRules: {
    '/': { prerender: true },
    '/dashboard/**': { ssr: true },
    '/api/**': { cache: { maxAge: 60 } }
  }
})
```

### 自動インポート

Nuxt の大きな特徴の一つ。コンポーネント・Composable・Vue API を `import` なしで使用できる：

```vue
<script setup>
// useRouter, useHead, useState 等を import 不要で使える
const router = useRouter();
</script>
```

### 2026年の方向性

- **Nuxt 4.3〜**: 細かな改善継続
- **Nuxt 5（Nitro v3）**: 完全に再設計されたサーバーエンジン。API の大幅変更はなく、実行性能と新機能に集中

## ポイント

- Vue.js エコシステムで最も普及したフルスタックフレームワーク
- Nitro による柔軟なデプロイ先（Node・サーバーレス・エッジ）
- 自動インポートにより開発体験が高く、ボイラープレートが少ない
- [[Next.js]] と機能的に対応しており、Vue ユーザーが最初に検討すべき選択肢
- [[Vite]] をビルドツールに採用し、高速な開発体験を提供

## 関連項目

- [[JavaScript]] — Nuxt のベース言語
- [[TypeScript]] — Nuxt が第一級サポートする型付け言語
- [[Vite]] — Nuxt のデフォルトビルドツール
- [[Next.js]] — React エコシステムにおける対応フレームワーク

## 参考

- [Nuxt 公式サイト](https://nuxt.com/)
- [Nuxt 4.0 アナウンス](https://nuxt.com/blog/v4)
- [Nuxt v4 ロードマップ](https://nuxt.com/blog/roadmap-v4)
