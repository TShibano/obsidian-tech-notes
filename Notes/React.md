---
title: "React"
date: 2026-03-10
tags:
  - Web
  - Frontend
  - Framework
related:
  - "[[JavaScript]]"
  - "[[TypeScript]]"
  - "[[Next.js]]"
  - "[[Vite]]"
---

## 概要

React は Meta（旧 Facebook）が開発した JavaScript 製の UI ライブラリ。コンポーネントベースのアーキテクチャと宣言的 UI 構築を特徴とし、2013年のオープンソース化以来フロントエンド開発のデファクトスタンダードとなった。2024年末に React 19 がリリースされ、React Compiler・Server Components・Server Actions が正式安定化した。2026年2月には React Foundation が Linux Foundation 傘下で設立。

## 詳細

### React 19 の主要機能

#### React Compiler（自動メモ化）

- `useMemo`・`useCallback`・`React.memo` の手動最適化が不要に
- コンパイラがコンポーネントを解析し、自動的に効率的な JS コードへ変換
- React 19 で本番利用可能（Production Ready）

#### React Server Components（RSC）

- サーバー上でのみ実行されるコンポーネント。ブラウザに JS を送らない
- データベースや内部 API に直接アクセス可能
- 初期ロード時の JS バンドルを削減（平均38%高速化）

```jsx
// Server Component（async も使える）
async function UserProfile({ id }) {
  const user = await db.users.find(id); // サーバー側で実行
  return <div>{user.name}</div>;
}
```

#### Server Actions

- フォーム送信・データミューテーションをサーバー関数として定義
- REST/GraphQL API の定型コードを削減

```jsx
async function updateName(formData) {
  'use server';
  await db.users.update({ name: formData.get('name') });
}
```

#### Concurrent Rendering（デフォルト有効）

- レンダリングを中断・一時停止できる仕組み。長いレンダリングでメインスレッドをブロックしない
- `useTransition`・`Suspense` と組み合わせて優先度の高い更新を先行処理

#### React 19.2 の新機能（2025年10月）

- **Activity コンポーネント**: アプリを「アクティビティ」単位に分割し、visible/hidden モードで制御

### バージョン履歴

| バージョン | リリース | 主要内容 |
|-----------|---------|---------|
| React 18 | 2022年3月 | Concurrent Mode 導入、`useTransition`・Suspense |
| React 19 | 2024年12月 | Compiler・RSC・Server Actions 安定化 |
| React 19.1 | 2025年6月 | バグ修正・改善 |
| React 19.2 | 2025年10月 | Activity コンポーネント追加 |

## ポイント

- JSX（HTML ライクな構文）を JavaScript に埋め込む宣言的 UI が特徴
- React Compiler により `useMemo`/`useCallback` の手動最適化が不要になりつつある
- Server Components と Server Actions によりフルスタック開発がより統合的に
- フレームワークとして使う場合は [[Next.js]] または Remix が推奨
- 単体ビルドツールとして [[Vite]] との組み合わせが一般的
- [[TypeScript]] での開発が実質的な標準

## 関連項目

- [[JavaScript]] — React の実装言語
- [[TypeScript]] — React 開発での標準的な型付け言語
- [[Next.js]] — React ベースのフルスタックフレームワーク
- [[Vite]] — React 開発に広く使われるビルドツール

## 参考

- [React 19.2 リリースブログ](https://react.dev/blog/2025/10/01/react-19-2)
- [React 公式ドキュメント](https://react.dev/)
- [React Foundation 設立（Linux Foundation）](https://react.dev/blog)
