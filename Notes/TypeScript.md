---
title: "TypeScript"
date: 2026-03-10
tags:
  - Language
  - Web
  - Frontend
  - Backend
related:
  - "[[JavaScript]]"
  - "[[React]]"
  - "[[Next.js]]"
  - "[[Vite]]"
---

## 概要

TypeScript は Microsoft が開発した JavaScript のスーパーセット言語。静的型付けと型推論をコンパイル時に適用し、大規模開発における保守性・安全性を高める。`.ts`/`.tsx` ファイルを JavaScript にトランスパイルして実行する。2026年現在、ネイティブ実装（TypeScript 7）への移行が進行中で、コンパイル速度が約10倍向上する見込み。

## 詳細

### TypeScript のバージョン推移

| バージョン | 時期 | 概要 |
|-----------|------|------|
| TypeScript 5.x | 2023〜2024 | 現行の安定版。デコレータ標準化、`const` 型パラメータ等 |
| TypeScript 6.0 | 2026年初 | JS ベース実装の最終版。将来の移行パスを整備 |
| TypeScript 7.0 | 2026年初 | **Go 実装（Project Corsa）による Native TypeScript**。約10倍の速度向上 |

### TypeScript 7 / Native TypeScript（Project Corsa）

2025年に発表された最大のプロジェクト。Microsoft が TypeScript コンパイラ（`tsc`）と言語サービスを Go で書き直す取り組み：

- **コンパイル速度**: 大規模コードベースで約10倍高速
- **プロジェクト読み込み**: 約8倍高速
- **型チェックの同等性**: 約20,000のコンパイラテストケースのうち74件のみ差異（99.6%の互換性）
- **strictness デフォルト有効**: TypeScript 7 から `strict` モードがデフォルトに（破壊的変更）
- **エディタ統合**: オートインポート、find-all-references、rename 等も移植済み

### 型システムの主要機能

```typescript
// ジェネリクス
function identity<T>(arg: T): T { return arg; }

// ユニオン型・交差型
type Status = 'active' | 'inactive';
type AdminUser = User & { permissions: string[] };

// テンプレートリテラル型
type EventName = `on${Capitalize<string>}`;

// 条件型・infer
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

// satisfies 演算子（TS 4.9〜）
const palette = { red: [255, 0, 0] } satisfies Record<string, number[]>;
```

### tsconfig のベストプラクティス（2026年）

- `"strict": true` を有効化（TypeScript 7 以降はデフォルト）
- `"moduleResolution": "bundler"` でバンドラー向け解決策を使用
- `"verbatimModuleSyntax": true` でインポートの型専用フラグを明示

## ポイント

- JavaScript に静的型付けを追加することで IDE の補完・型チェックを強化
- TypeScript 7（Project Corsa）により Go ネイティブ実装へ移行。速度が大幅向上
- React・Next.js・Vue など主要フレームワークはすべて TypeScript を第一級市民として扱う
- `strict: true` がデフォルトになることで型安全性の底上げが進む
- [[Rust]] の Turbopack（[[Next.js]] 採用）や [[Vite]] の esbuild など、周辺ツールチェーンが高速化している

## 関連項目

- [[JavaScript]] — TypeScript のベースとなる言語
- [[React]] — TypeScript での開発が標準化されているライブラリ
- [[Next.js]] — TypeScript を第一級サポートするフルスタックフレームワーク
- [[Vite]] — TypeScript を直接サポートするビルドツール

## 参考

- [TypeScript 公式ブログ: TypeScript 7 進捗報告（2025年12月）](https://devblogs.microsoft.com/typescript/progress-on-typescript-7-december-2025/)
- [Microsoft Developer Blog: TypeScript 7 Native Preview](https://developer.microsoft.com/blog/typescript-7-native-preview-in-visual-studio-2026)
- [TypeScript 公式サイト](https://www.typescriptlang.org/)
