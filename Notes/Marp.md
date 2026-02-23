---
title: "Marp"
date: 2026-02-23
tags:
  - Tool
  - CLI
related:
  - "[[Slidev]]"
---

## 概要

Marp（Markdown Presentation Ecosystem）は、Markdown ファイルからスライドを生成するオープンソースのプレゼンテーションツール。VSCode 拡張・CLI・フレームワークで構成され、Markdown で書いた文書を HTML・PDF・PowerPoint に変換できる。シンプルさと Git 管理を重視する開発者向けのプレゼン作成ツール。

## 詳細

### 基本情報

| 項目 | 内容 |
|------|------|
| 開発者 | Yuki Hattori（yhatt） |
| ライセンス | MIT |
| GitHub | github.com/marp-team/marp |
| フレームワーク | Marpit |
| 出力形式 | HTML、PDF、PPTX |

### Marp エコシステムの構成

```
Marpit（フレームワーク）
  └── marp-core（テーマ・拡張）
       ├── Marp CLI（コマンドライン）
       └── Marp for VS Code（エディタ統合）
```

- **Marpit**: Markdown → HTML/CSS スライドデッキを生成する軽量フレームワーク（プラグイン拡張可）
- **marp-core**: Marpit にデフォルトテーマ・数式・自動スケーリング等を追加
- **Marp CLI**: コマンドラインから変換を実行
- **Marp for VS Code**: リアルタイムプレビューと PDF エクスポートを VSCode で提供

### スライドの書き方

Markdown のドキュメントを `---` でページ分割する：

```markdown
---
marp: true
theme: default
---

# スライド 1

最初のスライドの内容

---

# スライド 2

次のスライドの内容

---

# コードブロックも普通に使える

```python
def hello():
    print("Hello, Marp!")
```
```

### フロントマター（グローバルディレクティブ）

```markdown
---
marp: true
theme: uncover
paginate: true
header: "My Presentation"
footer: "2026-02-23"
backgroundColor: "#fff"
---
```

| ディレクティブ | 説明 |
|-------------|------|
| `theme` | テーマ指定（default, uncover, gaia） |
| `paginate` | ページ番号表示 |
| `header` / `footer` | ヘッダー・フッターのテキスト |
| `backgroundColor` | 背景色 |
| `backgroundImage` | 背景画像 |

### ローカルディレクティブ（ページ単位）

```markdown
<!-- _backgroundColor: #000 -->
<!-- _color: white -->
<!-- _paginate: false -->

# このページだけ黒背景
```

アンダースコア付きのディレクティブは、そのページのみに適用される。

### Marp CLI

```bash
# インストール
npm install -g @marp-team/marp-cli

# HTML に変換
marp slides.md

# PDF に変換
marp slides.md --pdf

# PowerPoint に変換
marp slides.md --pptx

# ウォッチモード（変更を検知して自動変換）
marp --watch slides.md

# カスタムテーマを使用
marp --theme custom.css slides.md
```

### カスタムテーマ（CSS）

```css
/* custom.css */
@import 'default';  /* デフォルトテーマを継承 */

section {
  background-color: #1e1e2e;
  color: #cdd6f4;
  font-family: 'JetBrains Mono', monospace;
}

h1 {
  color: #89dceb;
  border-bottom: 2px solid #89dceb;
}

code {
  background-color: #313244;
}
```

### VS Code 統合

Marp for VS Code 拡張をインストールすると：
- フロントマターに `marp: true` を設定するだけでプレビューが有効化
- プレビューパネルとサイドバイサイドで編集
- PDF・HTML・PPTX への直接エクスポート

### Marp vs Slidev

| 観点 | Marp | [[Slidev]] |
|------|------|-----------|
| 対象ユーザー | Markdown 愛好家 | フロントエンド開発者 |
| 学習コスト | 低（Markdown 知識だけ） | 中（Vue/Vite の知識が役立つ） |
| カスタマイズ | CSS でテーマ変更 | Vue コンポーネント・Windi CSS |
| インタラクティブ性 | 低（静的）| 高（アニメーション・ライブコーディング） |
| 出力 | HTML/PDF/PPTX | HTML/PDF/PPTX/SPA |
| エディタ統合 | VS Code 拡張 | ブラウザベースのホットリロード |

## ポイント

- Markdown + `---` だけで完結するシンプルなプレゼン作成
- HTML・PDF・PowerPoint への出力に対応
- CSS でテーマをカスタマイズ可能（デフォルトテーマ 3 種あり）
- VS Code 拡張でリアルタイムプレビューが可能
- テキストファイルなので Git での版管理が自然に行える

## 関連項目

- [[Slidev]] - よりリッチなインタラクティブ性を持つ開発者向けプレゼンツール

## 参考

- [Marp 公式サイト](https://marp.app/)
- [GitHub - marp-team/marp](https://github.com/marp-team/marp)
- [Marp for VS Code - VS Marketplace](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode)
- [Marpit Framework](https://marpit.marp.app/)
