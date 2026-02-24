---
title: "Outline"
date: 2026-02-24
tags:
  - Tool
  - Web
related:
  - "[[Notion]]"
  - "[[HedgeDoc]]"
  - "[[Obsidian]]"
---

## 概要

Outline はチーム向けのオープンソース Wiki・ナレッジベースソフトウェア。Notion や Confluence のオープンソース代替として設計されており、セルフホストまたはクラウドホストで利用できる。モダンな UI・リアルタイム共同編集・豊富なインテグレーションを特徴とし、GitHub で 37,000 以上のスターを持つ。

## 詳細

### 基本情報

| 項目 | 内容 |
|------|------|
| 開発元 | General Outline, Inc. |
| ライセンス | BSL 1.1（Business Source License） |
| GitHub | outline/outline（37k+ stars） |
| 公式サイト | getoutline.com |
| 技術スタック | Node.js（TypeScript）・React・PostgreSQL |

### 主要機能

| 機能 | 説明 |
|------|------|
| リアルタイム共同編集 | 複数ユーザーが同時編集、変更が即時反映 |
| Markdown 互換 | Markdown 記法でのコンテンツ作成・インポート・エクスポート |
| 階層ドキュメント | コレクション → ドキュメント → サブドキュメントの階層構造 |
| バックリンク | ドキュメント間の被リンクを自動的に構築・表示 |
| 全文検索 | 全コンテンツを高速検索（日本語にも対応） |
| 権限管理 | 読み取り専用・編集・管理者などの細かな権限設定 |
| パブリック共有 | ドキュメントを URL 付きで外部公開可能 |

### インテグレーション

20以上の外部サービスと連携：

| カテゴリ | サービス |
|---------|---------|
| コミュニケーション | Slack（メンション・通知） |
| 認証（SSO） | Google、GitHub、Slack、SAML 2.0 |
| デザイン | Figma（埋め込み） |
| ビデオ | Loom |
| コード | GitHub、GitLab |
| その他 | Airtable、Framer、Marvel など |

### セルフホスト構成

Docker Compose での構成例：

```yaml
# 最小構成
services:
  outline:
    image: outlinewiki/outline
    env_file: .env
    ports:
      - "3000:3000"
  postgres:
    image: postgres:16
  redis:
    image: redis
```

主な環境変数：`DATABASE_URL`・`REDIS_URL`・`SECRET_KEY`・`AWS_S3_*`（ファイルストレージ）

### ライセンスについて

BSL 1.1（Business Source License）を採用。ソースコードは公開されているがコピーレフトではなく、以下の制約がある：

- セルフホストでの社内利用：無料
- マネージドサービスとして第三者への提供：要商用ライセンス
- 4年後に AGPL 3.0 に自動転換

### 他ツールとの比較

| ツール | 特徴 | 向き不向き |
|--------|------|-----------|
| **Outline** | チームWiki特化・OSS・セルフホスト可 | セキュリティ重視組織のナレッジベース |
| **[[Notion]]** | 多機能・DB統合・クラウド専用 | 多目的チームワークスペース |
| **[[HedgeDoc]]** | リアルタイム共同編集のみ・軽量 | 共同ドキュメント作成 |
| Confluence | エンタープライズ・Jira連携 | 大企業のプロジェクトドキュメント |

## ポイント

- Notion 代替としての OSS 実装の中では最も完成度が高く、UI も洗練されている
- バックリンク自動構築・ネスト構造により Obsidian 的なナレッジリンクも実現
- BSL ライセンスのため社内利用はセルフホストで無料、SaaS 的な第三者提供は商用ライセンスが必要
- Slack 連携が充実しており、チームチャットとナレッジベースの橋渡しとして機能

## 関連項目

- [[Notion]] - クラウド型オールインワンワークスペース
- [[HedgeDoc]] - リアルタイム共同 Markdown 編集
- [[Obsidian]] - ローカル完結型 Markdown ナレッジベース

## 参考

- [Outline 公式サイト](https://www.getoutline.com/)
- [GitHub - outline/outline](https://github.com/outline/outline)
- [Outline ドキュメント](https://www.getoutline.com/developers)
