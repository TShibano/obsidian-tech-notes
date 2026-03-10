---
title: "Next.js"
date: 2026-03-10
tags:
  - Web
  - Frontend
  - Backend
  - Framework
related:
  - "[[React]]"
  - "[[TypeScript]]"
  - "[[JavaScript]]"
  - "[[Vite]]"
---

## 概要

Next.js は Vercel が開発する React ベースのフルスタック Web フレームワーク。ファイルシステムベースのルーティング、複数のレンダリング戦略（SSG/SSR/ISR/PPR）、React Server Components の本番対応実装を提供する。2026年現在、JS 開発者の約68%が使用する最も普及した React フレームワーク。

## 詳細

### App Router（Next.js 13〜）

Next.js 13 で導入され、現在の推奨アーキテクチャ。`/app` ディレクトリ配下にファイルを置くだけでルートが定義される：

```
app/
├── layout.tsx          # 共有レイアウト（ネスト可）
├── page.tsx            # ルートページ（/）
├── dashboard/
│   ├── layout.tsx      # /dashboard 以下のレイアウト
│   └── page.tsx        # /dashboard ページ
└── api/
    └── route.ts        # API エンドポイント
```

### レンダリング戦略

| 戦略 | 説明 | 適用例 |
|------|------|--------|
| **SSG** (Static Site Generation) | ビルド時に HTML を生成 | ブログ、ドキュメント |
| **SSR** (Server-Side Rendering) | リクエストごとにサーバーで HTML 生成 | 認証が必要なページ |
| **ISR** (Incremental Static Regeneration) | 一定間隔でキャッシュを再生成 | 定期更新コンテンツ |
| **PPR** (Partial Pre-Rendering) | 静的シェル + 動的コンテンツのストリーミング | ほとんどのユースケース |

#### PPR（Partial Pre-Rendering）

Next.js 15 で導入。同一ページ内で静的部分と動的部分を混在させられる最新の最適解：

```jsx
import { Suspense } from 'react';

export default function Page() {
  return (
    <>
      <StaticHeader />        {/* 即時表示（静的シェル） */}
      <Suspense fallback={<Skeleton />}>
        <DynamicContent />    {/* ストリーミングで後から流れる */}
      </Suspense>
    </>
  );
}
```

### Turbopack（Next.js 15〜）

Rust 製バンドラー。Webpack を置き換え、大規模コードベースで約700倍高速：

- 開発サーバーの起動がほぼ瞬時
- HMR（Hot Module Replacement）が高速
- Next.js 15 でデフォルトバンドラーに昇格

### React Server Components との統合

Next.js は React Server Components (RSC) を最初に本番対応させたフレームワーク：

- デフォルトですべてのコンポーネントが Server Component
- クライアントコンポーネントは `'use client'` ディレクティブを付与
- Server Actions により API ルートなしにサーバー側処理を呼び出せる

## ポイント

- JS 開発者の約68%が使用。npm ダウンロード数が前年比約60%増
- App Router + RSC + PPR が 2026年のベストプラクティス
- Turbopack（Rust 製）により開発体験が大幅に向上
- [[React]] のメタフレームワーク的な位置づけ。React の上位互換で使える
- [[TypeScript]] のフルサポート。プロジェクト作成時にデフォルトで TypeScript を選択可能

## 関連項目

- [[React]] — Next.js のベースとなる UI ライブラリ
- [[TypeScript]] — Next.js での標準的な開発言語
- [[JavaScript]] — ベースとなる言語
- [[Vite]] — Next.js 以外の React 開発で広く使われるビルドツール
- [[Nuxt]] — Vue.js エコシステムでの対応フレームワーク

## 参考

- [Next.js 公式ドキュメント](https://nextjs.org/docs)
- [Next.js 公式サイト](https://nextjs.org/)
