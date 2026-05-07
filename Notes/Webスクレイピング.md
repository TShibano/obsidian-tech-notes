---
title: "Webスクレイピング"
date: 2026-05-07
tags:
  - Web
  - Tool
related:
  - "[[HTTP・HTTPS]]"
  - "[[Playwright]]"
  - "[[Python]]"
  - "[[データエンジニア]]"
---

## 概要

Webスクレイピングは，Web サイトから自動的にデータを収集・抽出する技術．API が提供されていないデータソースから情報を収集する手段として使われる．Python エコシステムに主要ツールが揃っている．

## 詳細

### スクレイピングのパターン

| パターン | 説明 | 使用ツール |
|---------|------|----------|
| **静的 HTML 解析** | サーバが HTML を返す従来型サイト | Requests + BeautifulSoup |
| **動的コンテンツ** | JavaScript でレンダリングされるコンテンツ | Selenium, Playwright |
| **大規模クロール** | サイト横断の多数ページ収集 | Scrapy |
| **API 経由** | 公式 API がある場合（最優先） | HTTPライブラリのみ |

### 主な Python ライブラリ（2026年時点）

| ライブラリ | 用途 | 特徴 |
|-----------|------|------|
| `Requests` | HTTP リクエスト | シンプル，静的コンテンツ向き |
| `BeautifulSoup4` | HTML/XML 解析 | 直感的な API |
| `Scrapy` | 大規模クロール | フレームワーク，ミドルウェア機構 |
| `Playwright` | ブラウザ自動化 | Chromium/Firefox/WebKit，非同期対応 |
| `Selenium` | ブラウザ自動化 | 古典的ツール，Web 自動テストにも使用 |
| `HTTPX` | HTTP クライアント | 非同期対応，HTTP/2 サポート |
| `selectolax` | HTML 解析 | BeautifulSoup より高速 |

### 技術的考慮事項

- **Bot 検出対策**: User-Agent 設定，レート制限（`time.sleep`），プロキシローテーション
- **JavaScript レンダリング**: SPA は静的解析できないため [[Playwright]] 等が必要
- **CAPTCHA**: 自動解決サービスや AI 解析（2Captcha 等）が使われるが利用規約確認必須
- **セッション管理**: Cookie / セッションの引き継ぎには `requests.Session` を使用

### 法的・倫理的注意事項

- **robots.txt を遵守する**: スクレイピング許可・禁止のルールが記載されている
- **利用規約（ToS）を確認する**: 禁止されていれば法的リスクがある
- **サーバ負荷を配慮する**: 過度なリクエストは DoS と見なされる場合がある
- **個人情報**: 個人情報を含むデータの収集・保存は法規制（GDPR 等）に注意

### データ収集後の処理

収集データは [[CSV]], [[JSON]], DB 等に保存し，[[データエンジニア]] がパイプラインで処理するケースが多い．

## ポイント

- 公式 API がある場合は必ずそちらを使う（安定・合法・高速）
- 動的コンテンツには [[Playwright]] が Selenium より現代的で推奨
- スクレイピングは Web サービスの ToS 違反になりやすいため事前確認が必須

## 関連項目

- [[HTTP・HTTPS]]
- [[Playwright]]
- [[データエンジニア]]

## 参考

- [Python Web Scraping Tutorial - Oxylabs](https://oxylabs.io/blog/python-web-scraping)
- [Best Web Scraping Tools 2026 - Scrapfly](https://scrapfly.io/blog/posts/best-web-scraping-tools)
