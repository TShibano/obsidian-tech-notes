---
title: "Swagger"
date: 2026-02-26
tags:
  - Web
  - API
  - Tool
related:
  - "[[API]]"
---

## 概要

SwaggerはREST APIの設計・ドキュメント化・テストを支援するオープンソースツール群。中核となる **OpenAPI Specification（OAS）** は、REST APIを人間とコンピューターの両方が読める形式（YAML/JSON）で記述する業界標準仕様であり、Swagger はこの仕様を活用したツールセットを指す。

## 詳細

### Swagger と OpenAPI の関係

もともと Swagger Specification として開発されたが、2016年に Linux Foundation 傘下の **OpenAPI Initiative** に寄贈され「OpenAPI Specification」と改称された。現在「Swagger」は SmartBear 社が提供するツール群の名称として使われる。

### Swagger ツール群

| ツール | 説明 |
|--------|------|
| **Swagger UI** | OAS ファイルからインタラクティブなAPIドキュメントを自動生成。ブラウザ上でAPIを試せる |
| **Swagger Editor** | ブラウザベースのOAS定義エディタ。リアルタイムバリデーション付き |
| **Swagger Codegen** | OAS定義からサーバースタブとクライアントライブラリを自動生成 |
| **Swagger Core** | JavaライブラリでOAS定義を作成・消費する |
| **Swagger Parser** | OAS定義ファイルを解析するスタンドアロンライブラリ |

### OpenAPI Specification（OAS）の構造

```yaml
openapi: "3.1.0"
info:
  title: "Data Pipeline API"
  version: "1.0.0"
paths:
  /pipelines:
    get:
      summary: "パイプライン一覧を取得"
      responses:
        "200":
          description: "成功"
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: "#/components/schemas/Pipeline"
components:
  schemas:
    Pipeline:
      type: object
      properties:
        id:
          type: string
        name:
          type: string
```

### API ファーストな開発フロー

1. OAS ファイルを先に定義（API ファースト）
2. Swagger UI でドキュメントを自動生成し、フロントエンドと合意
3. Swagger Codegen でサーバースタブを生成して実装を開始
4. テストは OAS ファイルをベースに自動化

### 現在のバージョン

- **OAS 3.1.0** が最新（JSON Schema との完全互換、Webhook サポートなど）
- **OAS 3.0.x** が広く普及している安定版
- **OAS 2.0**（旧 Swagger 2.0）も既存システムで利用されている

## ポイント

- **コードファーストvsAPIファースト**: 既存コードからOASを生成（コードファースト）か、OASを先に書いてコードを生成（APIファースト）かの2アプローチがある
- **インタラクティブドキュメント**: Swagger UI を使うことでドキュメントがそのままテスト環境になる
- **言語横断**: YAML/JSON で記述するため、バックエンドの実装言語に依存しない
- **エコシステム**: Postman, Redoc, Stoplight など多くのツールが OAS を読み込める

## 関連項目

- [[API]] — REST API の設計原則と Swagger の関係

## 参考

- [Swagger公式: OpenAPI Specification](https://swagger.io/specification/)
- [Swagger公式: What Is OpenAPI?](https://swagger.io/docs/specification/v3_0/about/)
