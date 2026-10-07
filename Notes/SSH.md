---
title: "SSH"
date: 2026-05-10
tags:
  - Security
  - Tool
  - DevOps
related:
  - "[[SCP]]"
  - "[[SFTP]]"
  - "[[ネットワーク]]"
  - "[[FTP]]"
  - "[[TLSサーバ証明書]]"
  - "[[Samba]]"
---

## 概要

SSH（Secure Shell）は，ネットワーク越しに安全なリモートアクセスと通信を提供するプロトコル（RFC 4251〜4256）．1995年にTatu Ylönenが開発し，安全でないTelnetやrshを置き換えた．通信の暗号化・認証・ポートフォワーディング等の機能を提供する．

## 詳細

### 仕組み

SSH 接続は3つのプロトコル層で構成される：

1. **トランスポート層プロトコル**: 暗号化，サーバ認証，整合性チェック（AES，ChaCha20，SHA-2等）
2. **ユーザ認証プロトコル**: クライアント認証（パスワード，公開鍵，GSSAPI等）
3. **接続プロトコル**: 単一のSSH接続上で複数の論理チャネル（シェル，ポートフォワード等）を多重化

### 認証方式

#### パスワード認証
ユーザ名とパスワードを暗号化チャネル越しに送信．ブルートフォース攻撃のリスクがあるため，公開鍵認証を推奨．

#### 公開鍵認証（推奨）
```
1. クライアントが秘密鍵 / 公開鍵ペアを生成
2. 公開鍵をサーバの ~/.ssh/authorized_keys に登録
3. 接続時: サーバがランダムなチャレンジを公開鍵で暗号化
4. クライアントが秘密鍵で復号し応答 → サーバが検証
```
- `ssh-keygen -t ed25519` で鍵ペア生成（ED25519 が現在推奨）
- `ssh-copy-id` で公開鍵をサーバに配布

### 主な機能

| 機能 | コマンド例 | 説明 |
|------|-----------|------|
| リモートシェル | `ssh user@host` | リモートでコマンド実行 |
| ファイル転送 | `scp`, `[[SFTP]]` | 安全なファイルコピー |
| ポートフォワーディング | `ssh -L 8080:localhost:80 host` | ローカル→リモートへのトンネル |
| リモートポートフォワーディング | `ssh -R 8080:localhost:80 host` | リモート→ローカルへのトンネル |
| SOCKS プロキシ | `ssh -D 1080 host` | 動的ポートフォワーディング |
| 多重化 | `ControlMaster` | 複数セッションを1接続で共有 |

### SCP と SFTP

- **[[SCP]]（Secure Copy）**: SSH上でのファイルコピー．`scp file user@host:/path`
- **[[SFTP]]（SSH File Transfer Protocol）**: FTPライクなファイル操作をSSH上で提供

### セキュリティのベストプラクティス

- パスワード認証を無効化（`PasswordAuthentication no`）
- root の直接ログインを禁止（`PermitRootLogin no`）
- 使用しない機能を無効化（X11 フォワーディング等）
- `fail2ban` による総当たり攻撃対策
- 非標準ポートへの変更（根本的な対策ではないが）

## ポイント

- RSAより ED25519（楕円曲線）が現在の推奨アルゴリズム（鍵が短く高速）
- SSH エージェント転送（`ssh -A`）は中間サーバ経由の多段 SSH を可能にするが，セキュリティリスクに注意
- Git の SSH 認証（`git@github.com:...`）や Ansible のリモート実行も SSH プロトコルを使用

## 関連項目

- [[SCP]]
- [[SFTP]]
- [[ネットワーク]]
- [[FTP]]
- [[TLSサーバ証明書]]
- [[Samba]]

## 参考

- [Secure Shell - Wikipedia](https://en.wikipedia.org/wiki/Secure_Shell)
- [SSH とは - e-Words](https://e-words.jp/w/SSH.html)
- [SSH（Secure Shell）の仕組み - Arcadia Academia](https://ar-aca.tech/posts/ssh-secure-shell-guide/)
