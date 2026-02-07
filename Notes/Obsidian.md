---
title: "Obsidian"
date: 2026-02-06
tags:
  - Tool
  - Editor
related:
  - "[[Markdown]]"
  - "[[Zettelkasten]]"
---

## 概要

Obsidian はローカルファーストの Markdown ベースのノートアプリケーション・個人知識管理（PKM）ツール。双方向リンクとグラフビューにより、ノート間の関係性を可視化し「リンクする思考」を実現する。個人利用は無料で、データは完全にユーザーの管理下にある。

## 詳細

### 基本情報

- **開発元**: Obsidian（Dynalist の創設者が設立）
- **対応OS**: macOS, Windows, Linux, iOS, Android
- **ライセンス**: 個人・商用利用は無料（クラウドサービスは有料）
- **最新バージョン**: v1.11.7（2026年2月5日）

### コア機能

#### ローカルファースト

ノートはデバイス上にプレーンテキスト（Markdown）として保存される。オフラインでも利用可能で、Obsidian が将来なくなっても他のツールでファイルを開ける。ベンダーロックインがない。

#### 双方向リンク

`[[ノート名]]` でリンクを作成すると、自動的にバックリンクが生成される。手動でインデックスを作る必要がなく、アイデア間の関係性をナビゲートできる。

#### グラフビュー

ノート間のリンクを視覚的に表示し、知識のパターンやギャップを発見しやすくする。

#### Canvas モード

ノート、画像、PDF を視覚的なボード上に配置できる。ブレインストーミングやプロジェクト計画に有用。

#### Bases（2026年新機能）

データベースライクなビューを作成できるコアプラグイン。ノートを動的なテーブルやマップとして表示可能。

### プラグインエコシステム

数千のコミュニティプラグインとテーマが利用可能：

| プラグイン | 用途 |
|-----------|------|
| Dataview | ノートのクエリ・集計 |
| Templater | 高度なテンプレート |
| Calendar | カレンダービュー |
| Kanban | タスク管理 |
| Excalidraw | 手書き風図解 |

### 有料サービス

| サービス | 月額 | 年額 | 機能 |
|----------|------|------|------|
| Obsidian Sync | $5 | $48 | 暗号化されたクラウド同期 |
| Obsidian Publish | $10 | $96 | ノートの Web 公開 |

### PKM ベストプラクティス

#### 代表的な方法論

- **Zettelkasten**: アトミックノート（1ノート1アイデア）とリンクによる知識ネットワーク
- **PARA**: Projects, Areas, Resources, Archives による分類
- **IMF**: Nick Milo の Index, Maps, Folders システム

#### 推奨ワークフロー

1. **Vault 構造の設計**: メジャーカテゴリごとにフォルダを作成
2. **アトミックノート**: 1つのアイデアを1つのファイルに
3. **積極的なリンク**: 関連するノートを `[[]]` で接続
4. **Daily Notes の活用**: 日次のアイデアキャプチャとジャーナリング
5. **ホームページの作成**: プロジェクトや優先事項へのナビゲーション用

### 長所と短所

**長所:**
- データの完全な所有権（ローカル Markdown ファイル）
- 高いカスタマイズ性（プラグイン・テーマ）
- オフライン利用可能
- 将来性のあるプレーンテキスト形式

**短所:**
- 学習曲線がある（Markdown に不慣れな場合）
- コラボレーション機能が限定的（個人向け）
- Web 版がない

## ポイント

- ローカルファーストでベンダーロックインなし
- 双方向リンクとグラフビューで「リンクする思考」を実現
- 数千のプラグインで高度にカスタマイズ可能
- 個人利用は完全無料
- PKM（個人知識管理）ツールとして高い評価

## 関連項目

- [[Markdown]] - Obsidian のファイル形式
- [[Zettelkasten]] - 代表的な PKM 方法論

## 参考

- [Obsidian - Sharpen your thinking](https://obsidian.md/)
- [Obsidian (software) - Wikipedia](https://en.wikipedia.org/wiki/Obsidian_(software))
- [Obsidian Changelog](https://obsidian.md/changelog/)
- [Releases - obsidianmd/obsidian-releases](https://github.com/obsidianmd/obsidian-releases/releases)
- [Knowledge management - Obsidian Forum](https://forum.obsidian.md/c/knowledge-management/6/l/top)
