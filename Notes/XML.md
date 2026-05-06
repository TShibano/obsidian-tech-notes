---
title: "XML"
date: 2026-05-06
tags:
  - Web
  - Backend
related:
  - "[[JSON]]"
  - "[[データフォーマット]]"
  - "[[CSV]]"
  - "[[JSONL]]"
---

## 概要

XML（Extensible Markup Language，拡張可能なマークアップ言語）はW3Cが策定したデータ記述言語．利用者がタグを自由に定義できる柔軟性が特徴で，企業間データ連携・設定ファイル・ドキュメント形式として広く使われてきた．

## 詳細

### 基本構文

```xml
<?xml version="1.0" encoding="UTF-8"?>
<employees>
  <employee id="1">
    <name>Alice</name>
    <department>Engineering</department>
    <salary>90000</salary>
  </employee>
  <employee id="2">
    <name>Bob</name>
    <department>Sales</department>
    <salary>75000</salary>
  </employee>
</employees>
```

### 主な特徴

- **拡張可能**: タグを自由に定義（HTML と異なり固定タグなし）
- **自己記述的**: データに意味を持たせる（メタデータ付き）
- **データとレイアウトの分離**: XSL でレイアウトを別管理
- **スキーマ定義**: DTD・XML Schema（XSD）で構造を厳密に定義可能

### XML関連技術

| 技術 | 用途 |
|------|------|
| XPath | XML 文書内のノードを指定するクエリ言語 |
| XSLT | XML を別フォーマット（HTML, PDF 等）に変換 |
| XSD | XML スキーマの定義（型・構造の検証） |
| SAX | ストリーミング方式の XML パーサ（低メモリ） |
| DOM | ツリー構造として XML をメモリ展開するパーサ |

### XMLの標準ボキャブラリ

| ボキャブラリ | 用途 |
|-------------|------|
| SOAP | Web サービスのメッセージ形式 |
| XBRL | 財務報告（有価証券報告書など） |
| SVG | ベクター画像の記述 |
| RSS/Atom | フィード配信 |
| SAML | シングルサインオン（認証アサーション） |

### JSONとの比較

| 項目 | XML | JSON |
|------|-----|------|
| 冗長性 | 高い（開始・終了タグ） | 低い |
| スキーマ | XSD で厳密定義 | JSON Schema（任意） |
| 名前空間 | あり | なし |
| 属性 | タグに属性を付与できる | なし（すべてフィールド） |
| 主な用途 | 企業間連携，ドキュメント | Web API，設定ファイル |

## ポイント

- Web API では JSON に置き換えられたが，エンタープライズ・金融・行政系では XML が現役
- SAML は XML ベースのため，SSO プロトコルとして現在も広く使われる
- 大規模データ処理では冗長なタグがオーバーヘッドになる

## 関連項目

- [[JSON]]
- [[データフォーマット]]
- [[CSV]]
- [[JSONL]]
- [[SSO]]

## 参考

- [Extensible Markup Language - Wikipedia](https://ja.wikipedia.org/wiki/Extensible_Markup_Language)
- [XML 入門 - MDN Web Docs](https://developer.mozilla.org/ja/docs/Web/XML/Guides/XML_introduction)
- [Extensible Markup Language (XML) - W3C](https://www.w3.org/XML/)
- [What is XML? - AWS](https://aws.amazon.com/what-is/xml/)
