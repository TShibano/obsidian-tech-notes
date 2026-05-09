---
title: "SCP"
date: 2026-05-09
tags:
  - Tool
  - Security
related:
  - "[[SFTP]]"
  - "[[FTP]]"
  - "[[ネットワーク]]"
---

## 概要

Secure Copy Protocol．SSH を利用してリモートサーバとのファイルコピーを安全に行うコマンド・プロトコル．シンプルで高速なファイル転送が特徴で，データエンジニアリングにおける運用自動化スクリプトでよく使われる．

## 詳細

### 基本的な使い方

```bash
# ローカル → リモート
scp localfile.txt user@remote_host:/remote/directory/

# リモート → ローカル
scp user@remote_host:/remote/path/file.txt ./

# ディレクトリごとコピー（-r オプション）
scp -r user@host:/remote/dir ./local_dir/

# デフォルト以外のポートを指定（-P オプション）
scp -P 2222 file.txt user@host:/path/
```

### SFTPとの比較

| | SCP | SFTP |
|---|---|---|
| 機能 | ファイルコピーのみ | ファイル管理全般 |
| 速度 | 高速（シンプル） | やや遅い（機能が豊富） |
| インタラクティブ操作 | 不可 | 可能 |
| 用途 | スクリプト自動化 | 対話的なファイル管理 |

### セキュリティ

- SSH の暗号化トンネル経由でデータを転送するため，盗聴・改ざんを防止
- パスワード認証と公開鍵認証の両方に対応
- SSH 設定（`~/.ssh/config`）を共有するため追加設定不要

### 注意点

- `scp` コマンドは一部のディストリビューションで非推奨となり，代替として `rsync` や `sftp` が推奨される場合がある
- サーバ側で SSH デーモン（sshd）が起動している必要がある

## ポイント

- シンプルな構文でスクリプトに組み込みやすい
- SSH の認証・暗号化をそのまま利用するため追加設定が不要
- 大量ファイルや差分転送には `rsync` の方が適する

## 関連項目

- [[SFTP]]
- [[FTP]]
- [[ネットワーク]]

## 参考

- [scpコマンドとは。セキュアなファイル転送の仕組み、使い方を解説 | ミライサーバーのススメ](https://www.miraiserver.ne.jp/column/about_scp/)
- [Secure copy protocol - Wikipedia](https://en.wikipedia.org/wiki/Secure_copy_protocol)
