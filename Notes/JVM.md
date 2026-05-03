---
title: "JVM"
date: 2026-05-03
tags:
  - Language
related:
  - "[[BeamVM]]"
  - "[[JavaScript]]"
---

## 概要

JVM（Java Virtual Machine）は Java バイトコードを実行する仮想マシンで、Java の「Write Once, Run Anywhere（WORA）」を実現する基盤。Sun Microsystems が 1995 年に開発し、現在は OpenJDK および Oracle JDK として提供される。Java のみならず Kotlin・Scala・Clojure・Groovy など多数の言語が JVM をターゲットとしており、豊富なエコシステムを持つ。

## 詳細

### JVM アーキテクチャ

```
ソースコード（.java / .kt / .scala）
    ↓ コンパイル
バイトコード（.class）
    ↓
JVM
├─ クラスローダーサブシステム
│   └─ Loading → Linking → Initialization
├─ ランタイムデータ領域
│   ├─ Method Area（クラス情報・スタティック変数）
│   ├─ Heap（オブジェクト・配列）← GC の対象
│   ├─ JVM スタック（スレッドごと）
│   ├─ PC レジスタ（スレッドごと）
│   └─ ネイティブメソッドスタック
└─ 実行エンジン
    ├─ インタープリタ
    ├─ JIT コンパイラ（ホットスポット最適化）
    └─ ガベージコレクタ
```

### クラスローダー

JVM はクラスを動的にロードする。三段階でクラスを初期化:
1. **Loading**: バイトコード（`.class`）をメモリに読み込む
2. **Linking**: バイトコードの検証・シンボル解決
3. **Initialization**: static 初期化ブロックの実行

### JIT コンパイル（HotSpot）

JVM はインタープリタで実行しながら「ホットスポット」（頻繁に実行されるコード）を検出し、ネイティブコードにコンパイルする。

| 最適化 | 説明 |
|--------|------|
| インライン展開 | 小さなメソッド呼び出しをインライン化 |
| エスケープ解析 | スタック割り当て可能なオブジェクトを判定 |
| ループアンローリング | ループ処理の最適化 |
| Dead Code 除去 | 実行されないコードを除去 |

GraalVM はさらに進んだ JIT を提供し、Ahead-of-Time（AOT）コンパイルにより起動時間を大幅短縮する。

### ガベージコレクション

JVM の GC は Heap 全体を管理するため、Stop-The-World（STW）が発生しうる。世代別 GC により若い世代（Eden・Survivor）と老世代（Old Gen）を区別して管理。

| GC | 特徴 | 適する用途 |
|----|------|------------|
| G1GC | 低遅延・予測可能な停止時間 | 汎用（Java 9+ デフォルト） |
| ZGC | 超低遅延（< 1ms 停止目標） | レイテンシ重視 |
| Shenandoah | ZGC に近い低遅延 | Red Hat 主導 |
| Serial/Parallel | シンプル・高スループット | バッチ処理 |

### JVM 言語エコシステム

| 言語 | 特徴 |
|------|------|
| Java | オリジナル言語。Java 25 LTS（2025）が現行 |
| Kotlin | Jetbrains 製。Android/サーバーで主流。Java と完全互換 |
| Scala | 関数型 + OOP。Spark・Akka の実装言語 |
| Clojure | Lisp 系関数型言語。イミュータブルデータ構造 |
| Groovy | 動的型付け。Gradle のビルドスクリプト |

### 2026 年の動向

- **Java 25 LTS**（2025年9月リリース）が現行の長期サポート版
- **Java 26**（2026年3月リリース）: Project Loom（Virtual Threads 安定版）の継続改善
- **GraalVM** の AOT コンパイル（Native Image）によるサーバーレス向け高速起動が普及
- **Project Valhalla**: 値型（Value Types）の導入による性能改善が進行中
- Virtual Threads（軽量スレッド）で並行処理モデルが大きく改善

### BEAM との比較

| 項目 | JVM | [[BeamVM]] |
|------|-----|-------|
| GC | Heap 共有・STW リスク | プロセス別・STW なし |
| 並行モデル | OS スレッド（Virtual Threads で改善） | アクターモデル（軽量プロセス） |
| 耐障害性 | 別途実装（Akka 等） | Supervisor ツリーが組み込み |
| 分散 | 別途フレームワーク | VM 内蔵 |
| エコシステム | 汎用・大規模 | 特化（通信・リアルタイム） |

## ポイント

- バイトコードと JIT コンパイルにより「Write Once, Run Anywhere」を実現
- HotSpot JIT がホットスポットをネイティブコードに最適化し、長時間稼働ほどパフォーマンスが向上する
- Heap 共有 GC は Stop-The-World リスクを持つが、ZGC/G1GC で大幅に改善
- Kotlin・Scala・Clojure など多数の言語が JVM をターゲットにする広大なエコシステム
- Virtual Threads（Project Loom）で軽量な並行処理が可能になり、[[BeamVM]] のプロセスモデルに近い設計が実現

## 関連項目

- [[BeamVM]] — Erlang/Elixir が動作する別系統の仮想マシン。並行処理モデルが対照的
- [[JavaScript]] — V8 エンジン（JIT コンパイル）で動作するスクリプト言語

## 参考

- [Java Virtual Machine - Wikipedia](https://en.wikipedia.org/wiki/Java_virtual_machine)
- [How JVM Works - JVM Architecture - GeeksforGeeks](https://www.geeksforgeeks.org/java/how-jvm-works-jvm-architecture/)
- [JVM Tutorial - freeCodeCamp](https://www.freecodecamp.org/news/jvm-tutorial-java-virtual-machine-architecture-explained-for-beginners/)
