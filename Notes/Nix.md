---
title: "Nix"
date: 2026-02-23
tags:
  - Tool
  - DevOps
related:
  - "[[NixOS]]"
  - "[[関数型プログラミング]]"
---

## 概要

Nix は純関数型パッケージマネージャーで、「同じ入力は同じ出力を生む」という関数型の原則でパッケージビルドと環境構築を管理する。再現性・原子的アップデート・複数バージョン共存・ロールバックを実現し、Linux・macOS（nix-darwin）で動作する。

## 詳細

### 基本情報

| 項目 | 内容 |
|------|------|
| 設計者 | Eelco Dolstra（2003年 博士論文で提唱） |
| 言語 | Nix 言語（純関数型の DSL）+ C++ |
| ライセンス | LGPL-2.1 |
| 対応 OS | Linux（含む [[NixOS]]）、macOS（nix-darwin） |
| 公式サイト | nixos.org |

### なぜ Nix が特別か

従来のパッケージマネージャー（apt, brew 等）はパッケージを `/usr/lib` などのグローバル空間にインストールし、同名・別バージョンのパッケージを共存させることができない。Nix はビルド入力（ソースコード・依存関係・ビルドフラグ等）のハッシュに基づき `/nix/store/<hash>-package-version/` にパッケージを格納するため：

- **再現性**: 同じ `flake.nix` / `default.nix` から常に同じバイナリが生成される
- **複数バージョン共存**: 異なるバージョンが別ハッシュとして共存する
- **原子的アップデート**: シンボリックリンクの切り替えでアップデートが行われ、失敗しても自動ロールバック
- **クリーンな環境**: ビルドはサンドボックスで実行され、未宣言の依存関係を検出できる

### Nix 言語

Nix は専用の純関数型言語（DSL）で記述する。遅延評価を採用し、パッケージのビルドレシピ（derivation）を定義する。

```nix
# 簡単な Nix 式の例
let
  pkgs = import <nixpkgs> {};
in pkgs.mkShell {
  buildInputs = [ pkgs.python3 pkgs.nodejs ];
}
```

### 主要な使い方

#### nix-shell / nix develop（開発シェル）

```bash
# 一時的に Python 環境を構築（グローバルインストール不要）
nix-shell -p python3 nodejs

# flake ベースの開発シェル
nix develop
```

プロジェクトに `shell.nix` または `flake.nix` を置けば、チーム全員が同一の開発環境を再現できる。

#### home-manager（ユーザー環境管理）

```nix
# ~/.config/home-manager/home.nix
{ config, pkgs, ... }: {
  home.packages = [ pkgs.ripgrep pkgs.bat pkgs.eza ];
  programs.git = {
    enable = true;
    userName = "Your Name";
  };
}
```

dotfiles を含むユーザー環境全体を Nix で宣言的に管理するツール。

#### nix-darwin（macOS 対応）

macOS 向けに [[NixOS]] のモジュールシステムを移植したもの。Homebrew を使わずに macOS のパッケージ管理・システム設定を宣言的に行える。

### Flakes（フレーク）

2021年から導入された（現在も experimental だが広く採用されている）現代的な Nix 設定方法。

```nix
# flake.nix の基本構造
{
  description = "My development environment";

  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-25.05";
    home-manager.url = "github:nix-community/home-manager";
  };

  outputs = { self, nixpkgs, home-manager, ... }: {
    # NixOS・nix-darwin・devShell などを定義
  };
}
```

Flakes の利点：
- `flake.lock` により入力バージョンを固定（Cargo.lock や package-lock.json に相当）
- 複数マシン（NixOS・macOS）の設定を一リポジトリで管理可能
- `nix run`・`nix build` での直接実行が容易

### Nix Store

```
/nix/store/
  abcdef123...-python3-3.12.0/
  ghijkl456...-python3-3.11.9/  # 別バージョンが共存
  mnopqr789...-ripgrep-14.1.0/
```

すべてのパッケージはここに格納される。ガベージコレクション（`nix-collect-garbage`）で未使用パッケージを削除できる。

### nixpkgs

Nix のパッケージコレクション。2026年現在、80,000 以上のパッケージを収録する世界最大のパッケージリポジトリ。

```bash
# パッケージを検索
nix search nixpkgs ripgrep
```

## ポイント

- 純関数型アプローチにより「私の環境では動く」問題を根本から解決
- グローバルインストールなし、依存関係コンフリクトなし、ロールバック可能
- `shell.nix`/`flake.nix` を共有するだけでチーム全員が同一環境を再現
- macOS でも nix-darwin を使えば Homebrew 依存から脱却できる
- Flakes + home-manager の組み合わせが 2025 年時点のモダンな使い方

## 関連項目

- [[NixOS]] - Nix を基盤とした宣言的 Linux ディストリビューション
- [[関数型プログラミング]] - Nix の設計原則（同じ入力→同じ出力）の理論的背景

## 参考

- [Nix & NixOS 公式サイト](https://nixos.org/)
- [Nix package manager - NixOS Wiki](https://wiki.nixos.org/wiki/Nix_(package_manager)/en)
- [home-manager Manual](https://nix-community.github.io/home-manager/)
- [NixOS and Flakes Book](https://nixos-and-flakes.thiscute.world/)
- [Nix Flakes と Home Manager を始める - Callista](https://callistaenterprise.se/blogg/teknik/2025/04/10/nix-flakes/)
