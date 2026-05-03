---
title: "BeamVM"
date: 2026-05-03
tags:
  - Language
related:
  - "[[Erlang]]"
  - "[[Elixir]]"
  - "[[Gleam]]"
---

## 概要

BEAM（Bogdan/Björn's Erlang Abstract Machine）は [[Erlang]] の仮想マシンであり，Erlang ランタイムシステム（ERTS）の核心部分．[[Erlang]]・[[Elixir]]・[[Gleam]] などの BEAM 言語をバイトコードにコンパイルし，高い並行性・耐障害性・分散処理を OS プロセス上で実現する．軽量プロセス・メッセージパッシング・スケジューラを内包し，「1コア1スケジューラ」で真の並列実行を実現する．

## 詳細

### アーキテクチャ概観

```
OS プロセス（1つ）
└─ BEAM VM
   ├─ スケジューラ×CPU コア数（真の並列実行）
   │   ├─ スケジューラ 1 → Erlang プロセス A, B, C...
   │   ├─ スケジューラ 2 → Erlang プロセス D, E, F...
   │   └─ ...
   ├─ I/O ポートドライバ
   ├─ GC（各プロセスが独自ヒープ）
   └─ 分散ノード管理（TCP/IP）
```

BEAM は OS スレッドではなく**軽量プロセス**（数KB）を使う．プロセス数は数百万規模で生成可能で，コンテキストスイッチは BEAM 内で完結するため OS のスレッドより圧倒的に軽量．

### プロセスモデル

各 BEAM プロセスは独立したメモリ（メールボックス・ヒープ・スタック）を持つ．プロセス間は**共有メモリなし**で，非同期メッセージパッシングのみで通信する．

| 項目 | 値 |
|------|-----|
| プロセス生成コスト | 数マイクロ秒 |
| プロセス初期ヒープ | ~2KB |
| 同時プロセス数 | 数百万（デフォルト上限 134 万） |
| スケジューリング | プリエンプティブ（リダクションカウントベース） |

### プリエンプティブスケジューリング

BEAM は「リダクション（reduction）」カウントに基づいてプロセスを切り替える．1リダクション ≈ 1命令実行で，2000 リダクションごとにスケジューラが次のプロセスへ切り替える．これにより1つの重いプロセスが全体をブロックしない．

### GC（ガベージコレクション）

各プロセスが独立したヒープを持つため，GC は**プロセスごとに独立**して実行される．1プロセスの GC が他プロセスを止めない．これが「Stop-The-World」が問題になる JVM との大きな差異で，レイテンシの安定性につながる．

### ホットコードリロード

稼働中のシステムのコードを無停止で更新できる．

```erlang
% 新しいモジュールをロードし，実行中プロセスに適用
code:load_file(my_module).
```

電話交換システム由来の機能．ダウンタイムなしのデプロイを VM レベルでサポートする．

### 分散機能

BEAM は VM 内蔵の分散モデルを持つ．ノード間の通信はローカルプロセスと同じ `!` 演算子で透過的に記述でき，ネットワーク障害からの自動復旧も組み込まれている．

### BEAM エコシステムの言語

| 言語 | 特徴 |
|------|------|
| [[Erlang]] | オリジナル言語．OTP が標準ライブラリ |
| [[Elixir]] | Ruby ライクな構文．Phoenix フレームワーク |
| [[Gleam]] | 静的型付け．Rust/TypeScript に近い構文 |
| LFE | Lisp 構文で書ける Erlang |
| Alpaca | ML 系の型システム |

### BEAM vs JVM

| 項目 | BEAM | JVM |
|------|------|-----|
| GC | プロセス別・非同期 | ヒープ共有・Stop-The-World リスク |
| 並行モデル | アクターモデル（メッセージパッシング） | スレッド（共有メモリ） |
| 耐障害性 | Supervisor ツリーが組み込み | 別途実装が必要 |
| 分散 | VM 内蔵 | 別途フレームワーク（Akka 等） |
| 言語 | Erlang/Elixir/Gleam など | Java/Kotlin/Scala/Clojure など |
| エコシステム | 特化（通信・リアルタイム） | 汎用・大規模 |

## ポイント

- 軽量プロセス（数KB）× 数百万 + プリエンプティブスケジューリング = 高い並行性と均一なレイテンシ
- プロセス別 GC により Stop-The-World が発生せず，レイテンシが安定する
- ホットコードリロードで無停止デプロイが VM レベルでサポートされる
- 内蔵の分散機能により複数ノードをクラスタリングして透過的に分散処理できる
- [[Elixir]] と [[Gleam]] の台頭で BEAM エコシステムへの参入者が増加している

## 関連項目

- [[Erlang]] — BEAM のオリジナル言語．OTP フレームワークを提供
- [[Elixir]] — BEAM 上で動作するモダンな関数型言語
- [[Gleam]] — BEAM 上で動作する静的型付き言語

## 参考

- [BEAM (Erlang virtual machine) - Wikipedia](https://en.wikipedia.org/wiki/BEAM_(Erlang_virtual_machine))
- [A brief introduction to BEAM - Erlang/OTP](https://www.erlang.org/blog/a-brief-beam-primer/)
- [The BEAM — Erlang Solutions](https://www.erlang-solutions.com/blog/the-beam-erlangs-virtual-machine/)
- [BEAM and JVM: Comparing and Contrasting — Erlang Solutions](https://www.erlang-solutions.com/blog/beam-jvm-virtual-machines-comparing-and-contrasting/)
