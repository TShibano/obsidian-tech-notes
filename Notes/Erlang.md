---
title: "Erlang"
date: 2026-05-03
tags:
  - Language
related:
  - "[[関数型プログラミング]]"
  - "[[Elixir]]"
  - "[[Gleam]]"
---

## 概要

Erlang は 1986 年に Ericsson が開発した並行・分散・耐障害性特化の関数型プログラミング言語。BEAM（Bogdan's Erlang Abstract Machine）仮想マシン上で動作し、「9つの9（99.9999999%）の可用性」を実現したスウェーデンの電話交換システムで実績を積んだ。現在も WhatsApp・RabbitMQ・CouchDB などの高可用性システムで採用されている。

## 詳細

### 設計思想: Let It Crash

Erlang の哲学は「エラーを防ぐ」のではなく「エラーが起きても復旧する」システム設計。

- プロセスは軽量（数KB）で、数百万プロセスを同時実行可能
- プロセスが異常終了しても Supervisor が自動再起動
- エラーは隣のプロセスに伝播しない（fault isolation）

```
Supervisor
├─ Worker プロセス A  ← クラッシュ → Supervisor が再起動
├─ Worker プロセス B
└─ Worker プロセス C
```

### アクターモデルによる並行処理

Erlang の並行処理はアクターモデルを採用。プロセスは**共有メモリを持たず**、メッセージパッシングのみで通信する。

```erlang
% プロセスの生成とメッセージ送信
Pid = spawn(fun() ->
  receive
    {hello, Name} ->
      io:format("こんにちは、~s~n", [Name])
  end
end),

Pid ! {hello, "世界"}.
```

- **spawn**: 軽量プロセスの生成
- **!**: メッセージ送信
- **receive**: メッセージ受信とパターンマッチ

### OTP（Open Telecom Platform）

OTP は Erlang の標準ライブラリ・設計パターン・ツールの集合体。実用的な Erlang 開発では OTP が必須。

| OTP コンポーネント | 説明 |
|-------------------|------|
| **GenServer** | 汎用サーバービヘイビア。状態管理と同期/非同期呼び出し |
| **Supervisor** | 子プロセスの監視・再起動戦略の管理 |
| **Application** | アプリケーションのライフサイクル管理 |
| **ETS/DETS** | インメモリ/ディスクの高速キーバリューストア |
| **mnesia** | 分散データベース |

### 分散システム

複数の Erlang ノードをクラスタリングし、透過的な分散処理が可能。同一コード内でローカル・リモートプロセスを同様に扱える。

```erlang
% 別ノードのプロセスに送信（ローカルと同じ構文）
{process_name, 'node@host'} ! {message, Data}
```

### 2026 年の動向

- Erlang/OTP 28.5 が 2026 年 4 月リリース（最新安定版）
- Erlang/OTP 29.0 RC3 が 2026 年 3 月リリース
- ICFP 2026 の衛星ワークショップとして Erlang Workshop 2026（第25回）開催予定
- [[Elixir]] と [[Gleam]] によって BEAM エコシステムの開発者層が拡大

## ポイント

- 軽量プロセス（数KB）を数百万単位で生成できる高並行性が最大の強み
- 「Let It Crash」哲学と Supervisor ツリーによる高い耐障害性
- 共有メモリなしのメッセージパッシングにより、デッドロックや競合状態が原理的に発生しない
- OTP を使うことで耐障害性のある本番システムを効率よく構築できる
- 構文の難解さを解消した [[Elixir]] と型安全な [[Gleam]] により BEAM エコシステムが広がっている

## 関連項目

- [[関数型プログラミング]] — Erlang が採用する関数型パラダイム
- [[Elixir]] — Erlang VM（BEAM）上で動作するモダンな言語
- [[Gleam]] — BEAM 上で動作する静的型付き言語

## 参考

- [Erlang/OTP 公式サイト](https://www.erlang.org/)
- [Erlang - Wikipedia](https://en.wikipedia.org/wiki/Erlang_(programming_language))
- [Concurrent Programming in Erlang](http://www1.erlang.org/erlang_book_toc.html)
