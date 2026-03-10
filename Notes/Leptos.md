---
title: "Leptos"
date: 2026-03-10
tags:
  - Language
  - Rust
  - Web
  - Frontend
  - Backend
  - Framework
related:
  - "[[Rust]]"
  - "[[関数型プログラミング]]"
  - "[[Tauri]]"
---

## 概要

Leptos は Rust 製のフルスタック・アイソモーフィック Web フレームワーク。細粒度リアクティビティ（Fine-Grained Reactivity）を核として宣言的 UI を構築する。クライアントサイドレンダリング（CSR）、サーバーサイドレンダリング（SSR）、SSR+Hydration をすべてサポートし、仮想 DOM を使わずにバニラ JS に次ぐ高いパフォーマンスを実現する。

## 詳細

### 細粒度リアクティビティ

Leptos の根幹をなす設計思想。React の再レンダリング（コンポーネント単位）や仮想 DOM 差分計算とは異なり、**Signal の変化が DOM の最小単位（テキストノード・属性・要素の有無）に直接伝播する**。

```rust
// シグナルを定義
let (count, set_count) = create_signal(0);

// シグナルを参照すると、変化したときその箇所だけ更新される
view! {
    <button on:click=move |_| set_count.update(|n| *n += 1)>
        "Count: " {count}
    </button>
}
```

`count` が変化したとき、ボタン内のテキストノードだけが更新される。他のコンポーネントや DOM ノードは一切再実行されない。

### アイソモーフィック設計

**サーバー関数（`#[server]`マクロ）** により、クライアントとサーバーのコードを同一ファイルで記述できる:

```rust
#[server]
async fn get_user(id: u32) -> Result<User, ServerFnError> {
    // この部分はサーバーでのみ実行される（DB アクセス等）
    db::fetch_user(id).await
}
```

クライアント側からは通常の非同期関数として呼び出すだけ。REST API のエンドポイント定義・手動の fetch 処理が不要。

### レンダリングモード

| モード | 説明 | 用途 |
|--------|------|------|
| CSR | ブラウザで Wasm が全レンダリング | SPA、インタラクティブなツール |
| SSR | サーバーで HTML を生成 | SEO 重視、初期表示速度 |
| SSR + Hydration | サーバーで HTML 生成 → ブラウザで Wasm を起動しインタラクティブ化 | 本番の多くのユースケース |
| Streaming SSR | HTML をストリームで逐次送信 | TTFB 最小化 |

### パフォーマンス

- 仮想 DOM 不使用：変更箇所のみ最小限更新
- Yew（他の Rust Web フレームワーク）より UI の作成・更新が大幅に高速
- Vue、Svelte、React を凌ぐベンチマーク結果（leptos.dev 掲載）
- Wasm バイナリは Rust の最適化コンパイルにより小さく保てる

### エコシステム・周辺技術

- **Actix-web / Axum**: サーバーサイドの HTTP フレームワークとして組み合わせて使用
- **leptos_router**: クライアントサイドルーティング
- **leptos_meta**: `<head>` タグ管理（SSR 時のメタデータ）
- **Tailwind CSS**: スタイリングで頻繁に組み合わせられる

### 開発状況（2025〜2026年）

API はほぼ安定しており、大きな破壊的変更は予定されていない。フルスタック Rust 開発を目指すコミュニティでの採用が増加中。

## ポイント

- 細粒度リアクティビティにより仮想 DOM のオーバーヘッドを排除。更新が極めて局所的で高速
- `#[server]` マクロによるサーバー関数で、フルスタック開発を単一コードベースで完結できる
- Rust のコンパイラがクライアント・サーバー両側の型安全性を保証する
- WebAssembly を通じてブラウザで Rust コードを実行するため、JS の知識があれば移行しやすい
- Tauri と組み合わせることで Rust 製デスクトップアプリのフロントエンドとしても利用可能

## 関連項目

- [[Rust]] — 実装言語
- [[関数型プログラミング]] — 宣言的 UI・リアクティビティの設計思想と親和性が高い
- [[Tauri]] — Rust 製デスクトップフレームワーク。Leptos をフロントエンドとして組み合わせ可能
- [[gpui]] — 同じく Rust 製 UI フレームワーク（ブラウザではなくネイティブ GPU レンダリング）

## 参考

- [Leptos 公式サイト](https://www.leptos.dev/)
- [Leptos Book（公式チュートリアル）](https://book.leptos.dev/)
- [GitHub: leptos-rs/leptos](https://github.com/leptos-rs/leptos)
- [Full Stack Rust with Leptos](https://benw.is/posts/full-stack-rust-with-leptos)
