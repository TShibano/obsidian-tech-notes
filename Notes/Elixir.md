---
title: "Elixir"
date: 2026-05-03
tags:
  - Language
  - Web
  - Framework
related:
  - "[[関数型プログラミング]]"
  - "[[Erlang]]"
  - "[[Gleam]]"
  - "[[BeamVM]]"
---

## 概要

Elixir は [[Erlang]] の BEAM VM 上で動作する関数型・並行処理向けのプログラミング言語．José Valim が 2012 年に開発し，Erlang の高い耐障害性・並行性を継承しつつ，Ruby ライクな親しみやすい構文とモダンなツールチェーンを提供する．Web フレームワーク Phoenix と組み合わせることでリアルタイム Web アプリ構築に強みを発揮する．

## 詳細

### Erlang との関係

Elixir は Erlang と完全に相互運用可能．Erlang の標準ライブラリ・OTP・Hex 上のパッケージをすべて利用できる．

```
Elixir コード
    ↓ コンパイル
BEAM バイトコード（Erlang と同一）
    ↓ 実行
BEAM VM（軽量プロセス・メッセージパッシング・Supervisor）
```

### 言語の特徴

```elixir
# パイプ演算子（|>）で処理を連鎖
"hello world"
|> String.split(" ")
|> Enum.map(&String.capitalize/1)
|> Enum.join(" ")
# => "Hello World"

# パターンマッチ
{:ok, result} = File.read("data.txt")
{:error, reason} = File.read("missing.txt")

# 関数定義とガード節
defmodule Math do
  def factorial(0), do: 1
  def factorial(n) when n > 0, do: n * factorial(n - 1)
end
```

| 特徴 | 説明 |
|------|------|
| パイプ演算子 | `\|>` でデータ変換を直感的に連鎖 |
| パターンマッチ | 変数束縛とデータ分解を同時に行う |
| マクロ | コンパイル時にコードを生成（メタプログラミング） |
| プロトコル | ポリモーフィズムを型クラス的に実現 |
| 軽量プロセス | [[Erlang]] と同様，数百万プロセスを生成可能 |

### OTP と Supervisor

Elixir は [[Erlang]] の OTP をファーストクラスでサポート．GenServer・Supervisor・Task・Agent などが標準ライブラリに含まれる．

```elixir
# GenServer の例
defmodule Counter do
  use GenServer

  def start_link(initial) do
    GenServer.start_link(__MODULE__, initial, name: __MODULE__)
  end

  def increment do
    GenServer.cast(__MODULE__, :increment)
  end

  def get do
    GenServer.call(__MODULE__, :get)
  end

  @impl true
  def handle_cast(:increment, count), do: {:noreply, count + 1}

  @impl true
  def handle_call(:get, _from, count), do: {:reply, count, count}
end
```

### Phoenix フレームワーク

Phoenix は Elixir 製の Web フレームワーク．Rails ライクな開発体験と BEAM の高い並行性を組み合わせる．

| 機能 | 説明 |
|------|------|
| **LiveView** | WebSocket を使いサーバーサイドでリアルタイム UI を管理（JS 不要） |
| **Channels** | WebSocket によるリアルタイム通信 |
| **Ecto** | データベースラッパー・クエリビルダー |
| **PubSub** | 分散メッセージング |

Phoenix LiveView は「バックエンドで状態管理しながらリアルタイムな UI を実現」するアプローチで，React を使わずにリアクティブな UI が構築できる．

### 主な採用事例

- **Discord**: 並行接続管理に Elixir を採用（数百万同時接続）
- **Fly.io**: プラットフォームのコア部分に採用
- **Changelog**: ポッドキャストプラットフォーム

### 2026 年の動向

- Elixir の並行性・耐障害性が予測不可能なレイテンシを許容できないチームの新標準として台頭
- Phoenix LiveView の成熟により，フルスタック Elixir 開発が現実的な選択肢に
- [[Gleam]] の台頭で BEAM エコシステム全体に型安全な開発の需要が高まっている

## ポイント

- Erlang の「Let It Crash」哲学と Supervisor による高可用性をモダンな構文で利用できる
- パイプ演算子（`|>`）による直感的なデータ変換が記述しやすく，可読性が高い
- Phoenix LiveView により WebSocket + サーバーサイド状態管理でリアクティブな UI を構築できる
- [[Erlang]] と完全互換のため，OTP の豊富なパターン（GenServer・Supervisor）がそのまま使える
- 静的型付けが必要な場合は同じ BEAM 上で動作する [[Gleam]] が選択肢になる

## 関連項目

- [[関数型プログラミング]] — Elixir が採用するプログラミングパラダイム
- [[BeamVM]] — Elixir が動作する BEAM 仮想マシンの詳細
- [[Erlang]] — Elixir が動作する BEAM VM の元祖言語
- [[Gleam]] — 同じ BEAM 上で動作する静的型付き言語

## 参考

- [Elixir 公式サイト](https://elixir-lang.org/)
- [Elixir - Wikipedia](https://en.wikipedia.org/wiki/Elixir_(programming_language))
- [Phoenix Framework 公式サイト](https://www.phoenixframework.org/)
- [Why Elixir Language, Erlang, BEAM, and Phoenix?](https://whyelixirlang.com/)
