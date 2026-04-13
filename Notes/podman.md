---
title: "Podman"
date: 2026-04-13
tags:
  - DevOps
  - Tool
  - Cloud
related:
  - "[[Docker]]"
  - "[[Kubernetes]]"
---

## 概要

Podman は Red Hat が開発したオープンソースのコンテナエンジンで、デーモンレス・ルートレスを特徴とする。OCI（Open Container Initiative）準拠のコンテナとイメージの管理ツールであり、Docker CLI と高い互換性を持ちながら、セキュリティと軽量性で優位に立つ。

## 詳細

### アーキテクチャの特徴

| 項目 | Docker | Podman |
|------|--------|--------|
| デーモン | dockerd が常駐（root 権限） | デーモンなし（各コンテナはユーザープロセスの子プロセス） |
| ルートレス | オプション（デフォルトは rootful） | デフォルトでルートレス |
| メモリ消費 | デーモンが 140-180MB 消費 | デーモン不要のため低い |
| 起動速度 | 標準 | 大規模ワークロードで最大30%高速 |
| ライセンス | Docker Desktop は商用利用で有料 | 完全無料 |

### デーモンレスアーキテクチャ

Docker は中央集権的なデーモン（dockerd）を介して全コンテナを管理するが、Podman はデーモンを持たない。各コンテナはユーザーセッションの子プロセスとして直接起動される。これにより：

- **単一障害点の排除**: デーモンがクラッシュしても他のコンテナに影響しない
- **セキュリティ向上**: root 権限で動作するデーモンソケットが不要
- **リソース効率**: アイドル時のリソース消費が削減される

### ルートレスコンテナ

Linux のユーザー名前空間（user namespaces）を利用し、一般ユーザー権限でコンテナを実行できる。コンテナ内の root ユーザーはホスト上の非特権ユーザーにマッピングされるため、コンテナエスケープ時のリスクが大幅に低減する。

### Pod のサポート

Podman は名前の通り「Pod」をネイティブにサポートする。Pod 内の複数コンテナはネットワーク名前空間を共有し、localhost で相互通信できる。この概念は [[Kubernetes]] の Pod と同一であり、K8s への移行準備として活用できる。

```bash
# Pod を作成してコンテナを追加
podman pod create --name my-app -p 8080:80
podman run -d --pod my-app nginx
podman run -d --pod my-app my-api-server
```

### Docker CLI との互換性

Podman のコマンド体系は Docker とほぼ同一であり、多くの場合エイリアスで置き換え可能：

```bash
alias docker=podman

# Docker と同じコマンドがそのまま使える
podman build -t my-app .
podman run -d -p 8080:80 my-app
podman ps
podman images
podman-compose up -d   # docker-compose 互換
```

### systemd 統合と Quadlet

Podman は systemd との統合が強力で、コンテナを Linux サービスとして管理できる：

- `podman generate systemd` で systemd ユニットファイルを自動生成
- **Quadlet**（Podman 4.4+）: `.container`、`.pod`、`.kube`、`.artifact` などの宣言的ファイル形式でコンテナをサービスとして定義
- systemd のライフサイクル管理（自動再起動、依存関係、ログ）をそのまま活用

### Podman Desktop

Podman Desktop は Windows・macOS 向けの GUI ツールで、Docker Desktop の代替として利用できる。コンテナ管理、イメージビルド、Kubernetes 連携などの機能を提供し、ライセンス制限がない。

### 最新動向（2026年）

- **Podman 5.x 系**: SQLite バックエンドへの移行（BoltDB は Podman 6.0 で廃止予定）
- **Quadlet の強化**: `.artifact` ファイルタイプ、テンプレート依存の対応
- **パフォーマンス改善**: `podman exec --no-session` オプション、VM 共有パスからのアーティファクト高速ロード
- **Visual Studio 2026**: Podman をコンテナ開発バックエンドとして公式サポート

## ポイント

- **Docker からの移行が容易**: CLI 互換性が高く、`alias docker=podman` でほぼそのまま使える
- **セキュリティファースト**: デーモンレス + ルートレスにより、デフォルトで Docker より安全
- **本番環境での活用**: Quadlet + systemd により、Kubernetes を使わない中小規模の本番デプロイにも適する
- **エンタープライズ採用拡大**: RHEL のデフォルトコンテナエンジンとして採用。Red Hat エコシステムとの親和性が高い
- **Docker Swarm 相当なし**: マルチノードオーケストレーションには [[Kubernetes]] が必要

## 関連項目

- [[Docker]] — Podman の互換対象となるコンテナエンジン。CLI が互換
- [[Kubernetes]] — Pod の概念を共有。Podman で作成した Pod 定義を K8s にエクスポート可能

## 参考

- [Podman 公式サイト](https://podman.io/)
- [Podman 公式ドキュメント](https://docs.podman.io/)
- [Red Hat: What is Podman?](https://www.redhat.com/en/topics/containers/what-is-podman)
- [GitHub: containers/podman](https://github.com/containers/podman)
- [Podman Desktop](https://podman-desktop.io/)
