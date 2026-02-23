---
title: "Zig"
date: 2026-02-23
tags:
  - Language
related:
  - "[[Rust]]"
  - "[[Ghostty]]"
  - "[[RedoxOS]]"
  - "[[Codeberg]]"
---

## 概要

Zig はシステムプログラミング向けの汎用言語で、C の現代的代替を目指す。「隠れた制御フローなし」「隠れたアロケーションなし」「コンパイル時メタプログラミング」を三大原則とし、C との完全な相互運用性を持つ。2024年末に開発リポジトリが GitHub から Codeberg へ移行し、1.0 は 2026〜2027 年を目標に開発中。

## 詳細

### 基本情報

| 項目 | 内容 |
|------|------|
| 設計者 | Andrew Kelley |
| 初版 | 2016年 |
| 最新版 | 0.14.x（安定版）※ 1.0 は 2026〜2027 年予定 |
| ライセンス | MIT |
| 公式サイト | ziglang.org |
| リポジトリ | codeberg.org/zig（2024年11月 GitHub から移行） |

### コア設計原則

#### 1. 隠れた制御フローなし（No Hidden Control Flow）

Zig コードが関数呼び出しのように見えなければ、それは関数を呼び出していない。演算子のオーバーロードなし、デストラクタなし、例外なし（エラーハンドリングは明示的な戻り値ベース）。

```zig
// エラーハンドリングは明示的
const result = try someFunction();  // エラー時は呼び出し元に伝搬（明示的）
```

#### 2. 隠れたアロケーションなし（No Hidden Allocations）

ヒープメモリ確保は常に明示的。標準ライブラリの関数はアロケータを引数として受け取り、どこでメモリが確保されるかがコードから明らかになる。

```zig
// アロケータを明示的に渡す
const ally = std.heap.GeneralPurposeAllocator(.{});
const list = try std.ArrayList(u8).init(ally.allocator());
defer list.deinit();
```

これにより、組み込みシステムなど限られたメモリ環境でも安全に使用できる。

#### 3. コンパイル時メタプログラミング（Comptime）

テキストプリプロセッサ（C の `#define` など）の代わりに、コンパイル時にコードを実際に実行する。型を値として操作でき、ジェネリクスや条件付きコンパイルを一貫した言語構文で表現できる。

```zig
// コンパイル時に型を受け取るジェネリック関数
fn max(comptime T: type, a: T, b: T) T {
    return if (a > b) a else b;
}
```

### C との相互運用性

C との相互運用は後付け機能ではなく基盤となる設計。

- C ヘッダを直接 `@cImport` で取り込める
- バインディングやラッパーなしで C ライブラリ関数を呼び出せる
- `zig cc` コマンドでクロスコンパイル対応の C/C++ コンパイラとして使用可能

```zig
const c = @cImport({
    @cInclude("stdio.h");
});

pub fn main() void {
    _ = c.printf("Hello from C library!\n");
}
```

### ビルドシステムとクロスコンパイル

`build.zig` による宣言的なビルドシステムを内蔵。特筆すべきは、すべてのサポートターゲット（Linux, macOS, Windows, FreeBSD, WASI 等）に対してクロスコンパイルが追加設定なしで可能なこと。

```bash
# Linux 向けに macOS からクロスコンパイル
zig build -target x86_64-linux-gnu
```

C プロジェクトのビルドシステムとしても使用でき、既存の C コードベースに Zig を段階的に導入できる。

### 採用事例

| プロジェクト | 用途 |
|------------|------|
| [[Ghostty]] | ターミナルエミュレータ（コア + Linux GUI） |
| Bun | JavaScript ランタイム（JavaScriptCore 統合等に Zig を使用） |
| TigerBeetle | 分散台帳データベース |

### Rust との比較

| 観点 | Zig | [[Rust]] |
|------|-----|---------|
| メモリ安全性 | 手動管理（安全ツール付き） | コンパイラが保証 |
| C との相互運用 | ネイティブ（ヘッダ直接取り込み） | FFI 経由 |
| コンパイル時間 | 速い | やや遅め |
| 学習曲線 | 緩やか（C の知識で入りやすい） | 急（所有権システム） |
| クロスコンパイル | ワンコマンド | cargo-cross 等が必要 |
| 安定版 | 未到達（1.0 準備中） | 2015年から安定版あり |

### 最新動向（2025〜2026年）

- **Codeberg 移行**: 2024年11月、GitHub から Codeberg へ
- **x86_64 バックエンド**: Debug ビルドのデフォルトバックエンドになり大幅高速化
- **コンパイラパイプライン並列化**: ビルド速度が向上
- **1.0 ロードマップ**: 言語仕様の凍結・安定 ABI・async/await 再設計・パッケージマネージャー 1.0 が目標

## ポイント

- C の「精神的後継」を目指す。C との完全な相互運用が設計の核心
- 隠れた制御フローなし・隠れたアロケーションなし・コンパイル時メタプログラミングが三大原則
- ワンコマンドで全プラットフォーム向けクロスコンパイルが可能
- `zig cc` を使えば既存の C/C++ プロジェクトのコンパイラとして利用可能
- Ghostty や Bun など著名プロジェクトが採用し始めており注目度が高い

## 関連項目

- [[Rust]] - 同じくシステムプログラミング言語。安全性保証方法が異なる
- [[Ghostty]] - Zig で書かれたターミナルエミュレータ
- [[RedoxOS]] - Rust 製 OS（Zig も OS 開発に使用されている）
- [[Codeberg]] - Zig の公式リポジトリが 2024年11月に移行した非営利 Git ホスティング

## 参考

- [Zig 公式サイト](https://ziglang.org/)
- [Zig Overview](https://ziglang.org/learn/overview/)
- [Zig Documentation](https://ziglang.org/documentation/master/)
- [GitHub → Codeberg 移行アナウンス](https://ziglang.org/devlog/2025/)
- [zig.guide](https://zig.guide/)
