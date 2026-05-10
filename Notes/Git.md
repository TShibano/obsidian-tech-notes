---
title: "Git"
date: 2026-05-10
tags:
  - Tool
  - DevOps
related:
  - "[[jujutsu]]"
  - "[[data version control]]"
  - "[[SSH]]"
  - "[[CI/CD]]"
---

## 概要

Gitは，Linus Torvaldsが2005年に開発した分散型バージョン管理システム（DVCS）．各開発者がリポジトリの完全なコピー（クローン）をローカルに保持し，オフラインでの操作，高速なブランチ操作，柔軟なワークフローを実現する．現在，事実上の業界標準VCS．

## 詳細

### 分散型の仕組み

- **ローカルリポジトリ**: 開発者が手元に持つリポジトリの完全なコピー
- **リモートリポジトリ**: GitHub / GitLab / Bitbucket等のサーバ側リポジトリ
- 中央サーバに依存せず，ほとんどの操作をローカルで完結できる（commit，log，diff等）

### データモデル

Gitのデータモデルはコンテンツアドレス型のオブジェクトグラフ：

| オブジェクト | 説明 |
|------------|------|
| Blob | ファイルの内容（SHA-1ハッシュで識別） |
| Tree | ディレクトリの構造（Blob / Tree への参照） |
| Commit | スナップショット（Tree + 親コミット + メタデータ） |
| Tag | コミットへのアノテーション付き参照 |

### 主要なコマンド

| 操作 | コマンド |
|------|---------|
| クローン | `git clone <url>` |
| 変更のステージング | `git add <file>` |
| コミット | `git commit -m "message"` |
| ブランチ作成 | `git branch <name>` / `git checkout -b <name>` |
| マージ | `git merge <branch>` |
| リベース | `git rebase <base>` |
| リモートへプッシュ | `git push origin <branch>` |
| リモートから取得 | `git pull` / `git fetch` |

### ブランチ戦略

| 戦略 | 概要 | 特徴 |
|------|------|------|
| Git Flow | main / develop / feature / release / hotfix | 大規模チーム，リリース管理が明確 |
| GitHub Flow | main + feature ブランチ | シンプル，CI/CD との親和性が高い |
| Trunk Based | main に小さく頻繁にコミット | 高速な継続デプロイ向け |

### [[jujutsu]] との比較

[[jujutsu]]（jj）はGitの代替として開発された新しいVCS（Gitバックエンドをサポート）．コンフリクト解決の改善，匿名ブランチ，変更セット（Change Set）ベースの思想が特徴．Git リポジトリ上でそのまま使える．

### データエンジニアリングでの活用

- **[[data version control]]（DVC）**: Gitと組み合わせてデータセット・モデルのバージョン管理
- **Pipeline as Code**: Airflow DAG，dbt モデルをGitで管理
- **GitOps**: インフラ状態をGitリポジトリで宣言的に管理

## ポイント

- コミットハッシュはコンテンツの SHA-1（または SHA-256）から生成されるため，改ざん検知ができる
- インタラクティブリベース（`git rebase -i`）でコミット履歴を整理できる
- `.gitignore` で追跡対象外ファイルを管理（シークレット，ビルド成果物等）

## 関連項目

- [[jujutsu]]
- [[data version control]]
- [[SSH]]

## 参考

- [Git - 分散作業の流れ - git-scm.com](https://git-scm.com/book/ja/v2/Git-%E3%81%A7%E3%81%AE%E5%88%86%E6%95%A3%E4%BD%9C%E6%A5%AD-%E5%88%86%E6%95%A3%E4%BD%9C%E6%A5%AD%E3%81%AE%E6%B5%81%E3%82%8C)
- [Git の仕組み - Qiita](https://qiita.com/haystacker/items/b2408940d11acd0fc674)
- [ブランチとは - Backlog](https://backlog.com/ja/git-tutorial/stepup/01/)
