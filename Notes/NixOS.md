---
title: "NixOS"
date: 2026-02-23
tags:
  - Tool
  - DevOps
related:
  - "[[Nix]]"
---

## 概要

NixOS は [[Nix]] パッケージマネージャーを基盤とした宣言的 Linux ディストリビューション。システム全体の設定を一つの `configuration.nix` ファイルで管理し、再現可能・ロールバック可能なシステム状態を実現する。「従来型 Linux ディストリビューション」の対極に位置するアプローチ。

## 詳細

### 基本情報

| 項目 | 内容 |
|------|------|
| 初版 | 2003年（Nix と同時期） |
| 最新 LTS | NixOS 25.05（2025年5月リリース） |
| カーネル | Linux |
| 公式サイト | nixos.org |
| 特徴 | 宣言的設定、原子的アップデート、ロールバック |

### 宣言的システム設定

NixOS の設定は `/etc/nixos/configuration.nix` に記述する。「システムがこうあるべき」という状態を宣言し、`nixos-rebuild switch` で実際にその状態を適用する。

```nix
# /etc/nixos/configuration.nix の例
{ config, pkgs, ... }:

{
  # ブートローダ
  boot.loader.systemd-boot.enable = true;

  # ネットワーク
  networking.hostName = "my-machine";
  networking.networkmanager.enable = true;

  # ユーザー
  users.users.alice = {
    isNormalUser = true;
    extraGroups = [ "wheel" "networkmanager" ];
    shell = pkgs.zsh;
  };

  # システムパッケージ
  environment.systemPackages = with pkgs; [
    git vim ripgrep bat
  ];

  # サービス
  services.openssh.enable = true;
  services.postgresql = {
    enable = true;
    package = pkgs.postgresql_16;
  };
}
```

### 原子的アップデートとロールバック

NixOS のアップデートはシンボリックリンクの切り替えによる原子的操作として行われる。GRUB/systemd-boot にはすべての世代が表示され、起動時に以前の状態に戻すことができる。

```bash
# システムを更新
nixos-rebuild switch

# 前の世代に戻す
nixos-rebuild switch --rollback

# 過去の世代を一覧表示
nix-env --list-generations --profile /nix/var/nix/profiles/system

# 古い世代を削除（ストレージ節約）
nix-collect-garbage -d
```

### Flakes によるモダンな管理

現代的な NixOS 管理では `flake.nix` を `/etc/nixos/` に配置し、バージョンロックと再現性を高める。

```nix
# flake.nix
{
  inputs = {
    nixpkgs.url = "github:NixOS/nixpkgs/nixos-25.05";
    home-manager = {
      url = "github:nix-community/home-manager/release-25.05";
      inputs.nixpkgs.follows = "nixpkgs";
    };
  };

  outputs = { self, nixpkgs, home-manager, ... }: {
    nixosConfigurations.my-machine = nixpkgs.lib.nixosSystem {
      system = "x86_64-linux";
      modules = [
        ./configuration.nix
        home-manager.nixosModules.home-manager
      ];
    };
  };
}
```

### モジュールシステム

NixOS の設定は再利用可能な「モジュール」で構成される。コミュニティが作成した多数のモジュールが利用可能で、サービス・デスクトップ環境・ハードウェアサポートなどが宣言的に設定できる。

```nix
# デスクトップ環境の設定例
services.xserver.enable = true;
services.xserver.displayManager.gdm.enable = true;
services.desktopManager.gnome.enable = true;
```

### home-manager との統合

[[Nix]] の home-manager を NixOS モジュールとして組み込むことで、システム設定とユーザー設定を一括管理できる。

### 他ディストリビューションとの比較

| 観点 | NixOS | 従来ディストリ（Ubuntu等） |
|------|-------|----------------------|
| 設定管理 | 宣言的（単一ファイル） | 手動・手続き的 |
| アップデート | 原子的（失敗なし） | パッケージ単位 |
| ロールバック | 容量無制限の世代管理 | 困難 |
| 再現性 | 完全（同じ設定→同じ状態） | 難しい |
| 学習曲線 | 急（Nix 言語習得が必要） | 緩やか |

### ユースケース

- **サーバー**: インフラをコードで管理（IaC）し、環境ドリフトを防ぐ
- **開発マシン**: dotfiles と開発ツールを flake で管理し、機種変後も即再現
- **CI/CD**: 再現可能なビルド環境をキャッシュしながら構築
- **homelab**: 複数サーバーの設定を 1 リポジトリで管理

### インストール方法

NixOS のインストールは従来型 Linux と同様に ISO からブートして行う。Disko 等のツールを使えばディスクパーティショニングも宣言的に記述できる。

## ポイント

- システム全体の状態を `configuration.nix` 一ファイルで宣言的管理
- 原子的アップデートにより途中失敗がなく、ロールバックは数秒で完了
- 同じ設定ファイルがあれば別マシンでも完全に同じシステムを再現できる
- Flakes + home-manager でユーザー環境含めた全環境をバージョン管理できる
- 学習コストは高いが、習得すれば運用の手間が大幅に削減される

## 関連項目

- [[Nix]] - NixOS の基盤となるパッケージマネージャー

## 参考

- [NixOS 公式サイト](https://nixos.org/)
- [NixOS Manual](https://nixos.org/manual/nixos/stable/)
- [NixOS Wiki](https://wiki.nixos.org/wiki/NixOS_system_configuration)
- [NixOS and Flakes Book](https://nixos-and-flakes.thiscute.world/)
- [How I like to install NixOS (declaratively) - Michael Stapelberg](https://michael.stapelberg.ch/posts/2025-06-01-nixos-installation-declarative/)
