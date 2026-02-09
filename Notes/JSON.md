---
title: "JSON"
date: 2026-02-09
tags:
  - Web
  - API
related:
  - "[[データフォーマット]]"
  - "[[YAML]]"
  - "[[TOML]]"
---

## 概要

JSON（JavaScript Object Notation）は、人間にも機械にも読みやすい軽量なテキストベースのデータ交換フォーマットである。2001年に Douglas Crockford によって考案され、現在では Web API の70%以上が採用する事実上の標準データ交換形式となっている。

## 詳細

### 歴史と仕様

JSON は ECMAScript（JavaScript）のオブジェクトリテラル記法に由来する。Douglas Crockford と Chip Morningstar が2001年4月に最初の JSON メッセージを送信したのが起源とされる。

仕様の変遷:

| 年 | 仕様 | 備考 |
|----|------|------|
| 2006 | RFC 4627 | Douglas Crockford による最初の Informational RFC |
| 2013 | ECMA-404 | ECMA International による標準化 |
| 2017 | RFC 8259（STD 90） | 現行のインターネット標準。UTF-8 エンコーディングを必須化 |

RFC 8259 では「クローズドエコシステム外でやり取りされる JSON テキストは UTF-8 でエンコードしなければならない（MUST）」という重要な規定が追加された。

### データ型と構文

JSON がサポートするデータ型は以下の6種類:

- **オブジェクト**: `{}` で囲まれたキー・バリューのペア
- **配列**: `[]` で囲まれた値のリスト
- **文字列**: ダブルクォートで囲まれたテキスト
- **数値**: 整数および浮動小数点数
- **真偽値**: `true` / `false`
- **null**: 空値

### 主な用途

#### Web API・REST API

公開 API の70%以上が JSON を主要データ形式として採用している。RESTful API の標準レスポンス形式として広く利用され、Twitter や Shopify などの大規模サービスでも採用されている。

#### 設定ファイル

`package.json`（Node.js）、`tsconfig.json`（TypeScript）、`.eslintrc.json` など、多くの開発ツールの設定ファイルに使われている。ただし、コメントが書けないという制限があるため、設定ファイル用途では [[YAML]] や [[TOML]] が好まれるケースもある。

#### ドキュメントデータベース

MongoDB や CouchDB などの NoSQL データベースは、JSON（BSON）をネイティブなデータ格納形式として採用している。PostgreSQL も `jsonb` 型で JSON データの格納・クエリをサポートしている。

#### モバイルアプリケーション

モバイルアプリケーションの約75%がクライアント・サーバー間通信に JSON を使用している。コンパクトな構造がリアルタイムデータ同期に適している。

#### データ交換・変換

CSV や PDF などの形式からの変換先として JSON が広く使われ、ERP・CRM・会計システムへのデータ連携に活用されている。

### 関連仕様・拡張

| 仕様 | 概要 |
|------|------|
| **JSON Schema** | JSON データの構造を定義・バリデーションするための仕様。現行バージョンは 2020-12 |
| **JSON-LD** | Linked Data を JSON で表現するための仕様。SEO の構造化データ（Schema.org）に広く利用 |
| **JSON5** | コメント、末尾カンマ、シングルクォート文字列など、JSON の制約を緩和した拡張仕様 |
| **JSON Lines（JSONL）** | 1行1 JSON オブジェクト形式。ストリーミング処理やログ出力に適する |

### JSON と XML の比較

| 観点 | JSON | XML |
|------|------|-----|
| 可読性 | 高い | 中程度（タグが冗長） |
| データサイズ | コンパクト | 冗長 |
| スキーマ | JSON Schema（任意） | XSD（強力） |
| コメント | 非対応 | 対応 |
| 名前空間 | なし | あり |
| 主な用途 | Web API、モバイル | エンタープライズ、SOAP |

## ポイント

- JSON は JavaScript のオブジェクトリテラル記法から生まれた軽量データ交換フォーマット
- RFC 8259（2017年）が現行のインターネット標準で、UTF-8 エンコーディングを必須としている
- Web API の70%以上、モバイルアプリの75%以上が JSON を採用しており、事実上の標準
- 6つの基本データ型（オブジェクト、配列、文字列、数値、真偽値、null）のみのシンプルな設計
- コメント非対応という制限があり、設定ファイル用途では [[YAML]] や [[TOML]] が選ばれることもある
- [[データフォーマット]]としては行指向・テキスト形式に分類され、分析用途では [[Apache Parquet]] などのバイナリ形式に劣る

## 関連項目

- [[データフォーマット]] - JSON を含むデータ形式の体系的な整理
- [[YAML]] - JSON のスーパーセットとして設計された設定ファイル向けフォーマット
- [[TOML]] - 設定ファイルに特化したシンプルなフォーマット

## 参考

- [RFC 8259 - The JavaScript Object Notation (JSON) Data Interchange Format](https://datatracker.ietf.org/doc/html/rfc8259)
- [ECMA-404 - The JSON Data Interchange Syntax](https://ecma-international.org/publications-and-standards/standards/ecma-404/)
- [JSON.org](https://www.json.org/)
- [JSON Schema](https://json-schema.org/)
- [JSON-LD - JSON for Linked Data](https://json-ld.org/)
- [MDN - Working with JSON](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Scripting/JSON)
