# Tech Notes Vault — Claude Code 指示書

## Vault の目的

技術トピックの学習ノートと日々のテックニュースを蓄積・整理するための Obsidian Vault。
Claude Code を活用して、ノート作成・ニュース収集を効率化する。

## フォルダ構成

```
tech-notes/
├── Notes/          # 技術ノート（トピック別）
├── DailyNews/      # デイリーニュースまとめ
├── Templates/      # テンプレート
├── Archives/       # アーカイブ
└── Attachments/    # 添付ファイル
```

---

## 共通ルール

- **言語**: 全ノート日本語で記述する
- **双方向リンク**: `[[ノート名]]` を積極的に使い、ノート間の関連を明示する
- **タグ管理**: frontmatter の `tags` フィールドのみで管理する（本文中の `#tag` は使わない）
- **日付形式**: `YYYY-MM-DD`（例: 2025-01-15）

---

## 技術ノート規約（Notes/）

### ファイル名

トピック名をそのままファイル名にする。

- 例: `Transformer.md`, `RAG.md`, `Docker.md`, `Next.js.md`

### Frontmatter

```yaml
title: "トピック名"
date: YYYY-MM-DD
tags:
  - カテゴリタグ
related:
  - "[[関連ノート]]"
```

### タグ体系

| 親タグ | サブタグ例 |
|--------|-----------|
| `AI` | `LLM`, `ML`, `Agent`, `NLP`, `ComputerVision` |
| `Web` | `Frontend`, `Backend`, `API`, `Framework` |
| `Cloud` | `AWS`, `GCP`, `Azure`, `K8s`, `Serverless` |
| `DevOps` | `CI/CD`, `IaC`, `Monitoring` |
| `Security` | `Auth`, `Encryption`, `ZeroTrust` |
| `DB` | `SQL`, `NoSQL`, `VectorDB` |
| `Language` | `Python`, `TypeScript`, `Go`, `Rust` |
| `Tool` | `Git`, `Editor`, `CLI` |

### 双方向リンク

- 関連する既存ノートへは必ず `[[ノート名]]` でリンクする
- まだ存在しないノートへも積極的にリンクする（ファントムリンク）
- リンク先のノートにも逆リンク（`related` への追記）を行い、双方向の整合性を保つ

### テンプレート

`Templates/tech-note.md` を使用する。

---

## デイリーニュース規約（DailyNews/）

### ファイル名

`YYYY-MM-DD.md`（例: `2025-01-15.md`）

### ニュースソース

**英語ソース:**
- Hacker News（https://news.ycombinator.com）
- TechCrunch（https://techcrunch.com）
- Ars Technica（https://arstechnica.com）
- The Verge（https://www.theverge.com）

**日本語ソース:**
- Publickey（https://www.publickey1.jp）
- Gigazine（https://gigazine.net）
- ITmedia（https://www.itmedia.co.jp）
- Zenn（https://zenn.dev）

### 論文ソース

- arXiv（https://arxiv.org）
  - 対象カテゴリ: `cs.AI`, `cs.CL`, `cs.CV`, `cs.LG`, `cs.MA`（必要に応じて他カテゴリも）
  - 直近数日以内に投稿・更新された論文から選別する
  - 3〜5本程度をピックアップする

### 選別基準

- **AI / ML** 関連を最優先で収集
- **Web / Cloud** 関連を次に重視
- その他の注目すべき技術ニュースも適宜収集

### 論文の選別基準

- 新しいアーキテクチャ・手法の提案
- 既存手法の大幅な性能改善（SOTA 更新）
- 実用性の高い応用研究（コード公開あり優先）
- 業界で話題になっている研究（SNS・Hacker News 等で注目）
- 著名な研究機関・企業からの発表

### リンクの原則

- **一次情報源のURLを使用する**（公式ブログ、公式発表、論文、プレスリリースなど）
- まとめ記事・ニュースアグリゲーターのURLは使わない（例: Zenn の週刊まとめ、Crescendo AI のニュースページなど）
- WebSearch で得た情報がまとめ記事経由の場合、追加検索で一次情報源を特定してからリンクする
- ソース列には一次情報源の発行元を記載する

### テンプレート

`Templates/daily-news.md` を使用する。

---

## ワークフロー

### 1. 技術ノート作成フロー

1. `Notes/` 内の既存ノートを確認し、重複がないかチェックする
2. WebSearch で最新情報を調査する
3. `Templates/tech-note.md` に従ってノートを作成する
4. 関連する既存ノートに双方向リンクを追加し、整合性を保つ

> スキル: `/tech-note <トピック名> [フォーカス指示]` で上記フローを実行できる

### 2. デイリーニュース作成フロー

1. 上記ニュースソースから当日の記事を取得する
2. AI/ML・Web/Cloud を中心にフィルタリングする
3. arXiv から注目論文をピックアップする
4. 各記事・論文を日本語で要約する
5. `Templates/daily-news.md` に従い、カテゴリ別テーブルにまとめる

### 3. ニュース → 技術ノート展開フロー

1. デイリーニュースの中から深掘りしたい記事を選ぶ
2. WebSearch で追加調査を行い、`Notes/` に技術ノートを作成する
3. 元のデイリーニュースの該当行に `📝 → [[ノート名]]` を追記する

### 4. リサーチキュー処理フロー

GitHub Issue のチェックリストに調査項目を列挙し、一括処理する:

1. Issue にチェックリスト形式（`- [ ] トピック名 (フォーカス指示)`）で調査項目を記載
2. `/research-queue [issue番号] [最大処理数]` でスキルを実行
3. 未完了項目を順次処理し、`Notes/` に技術ノートを作成
4. 完了した項目は Issue から削除

> スキル: `/research-queue [issue番号] [最大処理数]` で上記フローを実行できる
> - 省略時: Issue #1 から最大3件処理
