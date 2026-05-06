---
title: "CSV"
date: 2026-05-06
tags:
  - Tool
  - DB
related:
  - "[[データフォーマット]]"
  - "[[JSONL]]"
  - "[[XML]]"
---

## 概要

CSV（Comma-Separated Values）はカンマ区切りでレコードを表現するテキストベースのデータフォーマット．仕様は RFC 4180 で定義されており，スプレッドシート・DB・データ分析ツールで広くサポートされるデータ交換形式．

## 詳細

### RFC 4180 仕様の主要ルール

- 各レコードは CRLF で区切る
- 先頭行はオプションのヘッダ行
- フィールドの区切り文字はカンマ（`,`）
- カンマ・改行・ダブルクォートを含むフィールドはダブルクォートで囲む
- フィールド内のダブルクォートは2つのダブルクォート（`""`）でエスケープ
- MIME タイプ: `text/csv`

```csv
name,age,city
Alice,30,"New York"
Bob,25,Tokyo
"Charlie, Jr.",35,"San Francisco"
```

### CSVの利点と欠点

| 観点 | 内容 |
|------|------|
| 利点 | 人間が読みやすい，あらゆるツールで開いてられる，実装が簡単 |
| 欠点 | 型情報なし（すべて文字列），スキーマなし，大規模データでは非効率 |
| 欠点 | カンマ・改行・エンコーディングの扱いが実装によって異なる |

### データエンジニアリングでの位置づけ

CSVは取り込みレイヤーや外部連携での入力フォーマットとしてよく使われるが，内部処理・保存には Parquet や JSONL のほうが効率的．

| 用途 | 推奨フォーマット |
|------|---------------|
| 外部システムとのデータ交換 | CSV |
| 大規模バッチ処理・分析 | Apache Parquet |
| ストリーミング・ログ | JSONL |

## ポイント

- RFC 4180 は「informational」であり強制力はなく，実装差異が多い（Shift-JIS問題など）
- 文字コードの扱い（UTF-8 vs Shift-JIS）が日本語環境で課題になりやすい
- タブ区切り（TSV）も CSV の変種として同じ文脈で扱われることが多い

## 関連項目

- [[データフォーマット]]
- [[JSONL]]
- [[XML]]
- [[Apache Parquet]]

## 参考

- [RFC 4180 - Common Format and MIME Type for Comma-Separated Values (CSV) Files](https://www.rfc-editor.org/rfc/rfc4180.html)
- [RFC 4180 日本語訳](https://tex2e.github.io/rfc-translater/html/rfc4180.html)
- [CSVファイルの標準仕様と運用時の注意点をまとめてみた](https://search-bank.net/article.php?id=484)
