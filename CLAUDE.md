# Tech Notes Vault — Claude Code 指示書

## Vault の目的

技術トピックの学習ノートと日々のテックニュースを蓄積・整理するための Obsidian Vault．
Claude Code を活用して，ノート作成・ニュース収集を効率化する．

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
- **双方向リンク**: `[[ノート名]]` を積極的に使い，ノート間の関連を明示する
- **タグ管理**: frontmatter の `tags` フィールドのみで管理する（本文中の `#tag` は使わない）
- **日付形式**: `YYYY-MM-DD`（例: 2025-01-15）

### バージョン管理（グローバル設定の jj ルールを上書き）

- この Vault では **jj を使わない**．`jj` コマンドは一切実行せず，`jj new` / `jj describe` / bookmark / PR フローも適用しない
- バージョン管理は Obsidian Git 拡張機能（`vault backup: ...` の自動コミット）と素の `git` で行う
- 作業ブランチは `main` のみ．ブランチや PR は作らず，各スキルの手順どおり `git add` → `git commit` → `git push` で `main` に直接反映する
- Obsidian Git の自動コミットと競合しないよう，履歴の書き換え（`rebase`，`commit --amend`，`push --force`）はしない

---

## 技術ノート規約（Notes/）

### ファイル名

トピック名をそのままファイル名にする．

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
- リンク先のノートにも逆リンク（`related` への追記）を行い，双方向の整合性を保つ

### テンプレート

`Templates/tech-note.md` を使用する．

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

### 脆弱性ソース

- CISA Known Exploited Vulnerabilities Catalog（https://www.cisa.gov/known-exploited-vulnerabilities-catalog）
- NVD（https://nvd.nist.gov）
- JVN（https://jvn.jp）/ JPCERT/CC（https://www.jpcert.or.jp）/ IPA（https://www.ipa.go.jp/security/）
- GitHub Advisory Database（https://github.com/advisories）
- 各ベンダーのセキュリティアドバイザリ（Microsoft MSRC，Apple，Chrome Releases 等）

### 脆弱性の選別基準

- 調査対象は Vault ルートの `vuln-watchlist.md` に箇条書き（`- 用語` または `- 用語 (補足)`）で列挙した用語（Linux，Python，Rust，GitLab 等）に関係するものだけに絞る．スキルはこのファイルを編集しない
- 対象期間は前回の DailyNews 以降（前回が30日より前なら直近14日）．前回掲載済みの CVE は載せない
- 実際の悪用が確認されているもの（CISA KEV 追加，ベンダーが悪用を明言）と CVSS 7.0 以上（High / Critical）を載せる
- CVSS は出典（NVD / ベンダー）を明記する

### 選別基準

デイリーニュースのセクションは「AI / ML」「プログラミング言語・開発ツール」「データビジネス」「脆弱性」「注目論文（arXiv）」の5つ．

- **AI / ML**: AI・機械学習のニュースを最優先で収集
- **プログラミング言語・開発ツール**: Python / Rust / Julia，エディタ（VS Code，Zed，Helix），開発支援ツール（Claude Code 等の AI コーディングツール）のリリースと更新内容を，前回の DailyNews 以降の分まとめて要約する
- **データビジネス**: 国内外の企業のデータビジネスの動き．特に新しいデータ基盤を導入・刷新して改善した事例を優先する（企業の技術ブログ・プレスリリースを一次情報源とする）

### 論文の選別基準

- 新しいアーキテクチャ・手法の提案
- 既存手法の大幅な性能改善（SOTA 更新）
- 実用性の高い応用研究（コード公開あり優先）
- 業界で話題になっている研究（SNS・Hacker News 等で注目）
- 著名な研究機関・企業からの発表

### リンクの原則

- **一次情報源のURLを使用する**（公式ブログ，公式発表，論文，プレスリリースなど）
- まとめ記事・ニュースアグリゲーターのURLは使わない（例: Zenn の週刊まとめ，Crescendo AI のニュースページなど）
- WebSearch で得た情報がまとめ記事経由の場合，追加検索で一次情報源を特定してからリンクする
- ソース列には一次情報源の発行元を記載する
- デイリーニュースに限り，一次情報源が見つからないが興味深いニュースは，報道機関の記事を URL に使い，ソース列に「（報道）」，要約の先頭に「【一次情報未確認】」を付けて掲載してよい（今後の調査の手がかりとして残すため）

### テンプレート

`Templates/daily-news.md` を使用する．

---

## ワークフロー

### 1. 技術ノート作成フロー

1. `Notes/` 内の既存ノートを確認し，重複がないかチェックする
2. WebSearch で最新情報を調査する
3. `Templates/tech-note.md` に従ってノートを作成する
4. 関連する既存ノートに双方向リンクを追加し，整合性を保つ

> スキル: `/tech-note <トピック名> [フォーカス指示]` で上記フローを実行できる

### 2. デイリーニュース作成フロー

1. Sonnet サブエージェントが分野別（AI/ML，プログラミング言語・開発ツール，データビジネス，脆弱性，arXiv 論文）に並列で候補を収集する
2. Opus が掲載候補の一次情報源をすべて再取得して照合し，選別・要約を確定する（最終的な正しさは Opus が責任を持つ）
3. 各記事・脆弱性・論文を日本語で要約する
4. `Templates/daily-news.md` に従い，カテゴリ別テーブルにまとめる

> スキル: `/daily-news [YYYY-MM-DD]` で上記フローを実行できる

### 3. ニュース → 技術ノート展開フロー

1. デイリーニュースの中から深掘りしたい記事を選ぶ
2. WebSearch で追加調査を行い，`Notes/` に技術ノートを作成する
3. 元のデイリーニュースの該当行に `📝 → [[ノート名]]` を追記する

### 4. リサーチキュー処理フロー

GitHub Issue のチェックリストに調査項目を列挙し，一括処理する:

1. Issue にチェックリスト形式（`- [ ] トピック名 (フォーカス指示)`）で調査項目を記載
2. `/research-queue [issue番号] [最大処理数]` でスキルを実行
3. 未完了項目を順次処理し，`Notes/` に技術ノートを作成（同名ノートが既にある場合は内容を確認して最新情報に更新）
4. 完了した項目は Issue から削除

> スキル: `/research-queue [issue番号] [最大処理数]` で上記フローを実行できる
> - 省略時: Issue #1 から最大3件処理

### 4.5. ノート最新化フロー

既存ノートを当日時点の最新情報に更新する:

1. Vault ルートの `update-queue.md` に更新したいノート名を箇条書き（`- ノート名` または `- ノート名 (重点観点)`）で記載
2. `/update-note` でスキルを実行
3. Sonnet サブエージェントがノートごとに並列で調査・下書きし，Opus が精査して，既存構成を保ったまま古い記述を修正・新情報を追記する
4. frontmatter に `updated: YYYY-MM-DD` を付け，末尾の `## 更新履歴` に変更点を追記
5. `update-queue.md` は編集しない．毎回，記載されたノートをすべて処理する

> スキル: `/update-note` で上記フローを実行できる

### 5. Book コンパイルフロー

Books/ の読書メモを解析し，トピック単位で Notes/ に知識を展開する（LLM Wiki のコンパイルパターン）:

1. Books/ のマークダウンファイルを読み取り，章構造とトピックを解析
2. 各トピックを Notes/ の既存ノートにマッピング（更新 or 新規作成）
3. マッピング結果をユーザーに提示し，承認を得る
4. ノートの作成・更新を実行（必要に応じて WebSearch で補足）
5. 双方向リンクの整合性を維持
6. Books/ ファイルにコンパイル実施記録を追記

> スキル: `/compile-book <Books/のファイルパス> [対象章の指定]` で上記フローを実行できる

### 6. Vault Lint（健全性チェック）フロー

Notes/ を中心に Vault 全体の整合性を検査する（LLM Wiki のメンテナンスパターン）:

- **links**: related の非対称検出，壊れたリンク検出，孤立ノート検出
- **frontmatter**: 必須フィールド欠落，タグ体系の逸脱
- **content**: 空セクション検出，Books 未展開トピック検出

> スキル: `/lint [モード] [カテゴリ]` で上記フローを実行できる
> - `/lint` — 全チェックのレポート出力
> - `/lint fix` — 自動修正可能なものを修正
> - `/lint links` / `/lint frontmatter` / `/lint content` — カテゴリ指定
