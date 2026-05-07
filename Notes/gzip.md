---
title: "gzip"
date: 2026-05-06
tags:
  - Tool
  - DevOps
related:
  - "[[データフォーマット]]"
  - "[[Apache Parquet]]"
  - "[[bzip2]]"
  - "[[Snappy]]"
  - "[[圧縮]]"
---

## 概要

gzipはDeflateアルゴリズム（LZ77 + ハフマン符号化）を使用した汎用圧縮フォーマット・ツール．Unixシステムで広く普及しており，Web配信・ログ圧縮・データパイプラインなど幅広い場面で使われる．

## 詳細

### 仕様

- アルゴリズム: Deflate（LZ77 + ハフマン符号化）
- RFC 1952 で定義
- 拡張子: `.gz`
- MIME タイプ: `application/gzip`

### 特性

| 特性 | 評価 | 詳細 |
|------|------|------|
| 圧縮率 | 中〜高 | bzip2 より低いが十分実用的 |
| 圧縮速度 | 速い | -1（最速）〜 -9（最高圧縮）でレベル調整可能 |
| 解凍速度 | 速い | ストリーミング解凍が可能 |
| 分割圧縮 | 不可 | ランダムアクセス非対応 |
| CPU 使用率 | 中程度 | |

### 他フォーマットとの比較

| フォーマット | アルゴリズム | 圧縮率 | 速度 | 用途 |
|------------|------------|--------|------|------|
| gzip | Deflate | 中 | 速い | Web 配信，ログ，汎用 |
| bzip2 | BWT + ハフマン | 高 | 遅い | アーカイブ，Hadoop |
| Snappy | LZ ベース独自 | 低 | 非常に速い | Parquet/ORC の内部圧縮 |
| zstd | Zstandard | 高 | 非常に速い | 現代的な汎用圧縮 |
| lz4 | LZ4 | 低 | 最速クラス | リアルタイム処理 |

### データエンジニアリングでの利用

- **CSV/JSON + gzip**: 外部連携・S3 保存の一般的な組み合わせ
- **Parquet + gzip**: 列指向フォーマットとの組み合わせ（互換性重視）
- **HTTP 通信**: `Content-Encoding: gzip` でレスポンス圧縮

## ポイント

- 分割圧縮できないため，Hadoop では非効率（ブロック分割後の並列処理ができない）
- Parquet の圧縮コーデックとして Snappy が主流だが，圧縮率を重視する場合は gzip も選択される
- Web 配信では brotli に置き換えられつつあるが，後方互換性の高さから依然多用される

## 関連項目

- [[データフォーマット]]
- [[Apache Parquet]]
- [[bzip2]]
- [[Snappy]]
- [[圧縮]]

## 参考

- [圧縮ツールの比較: gzip, bzip2, lzop, lz4, xz, zstd](https://zenn.dev/masaki_wk/articles/20240406-compressors-comparison)
- [ファイルの圧縮形式ガイド ― 転送コストを抑える選び方](https://qiita.com/ktdatascience/articles/a32858428490d8a9bde8)
- [今更だけど，データ圧縮についてまとめてみたい](https://www.plan-b.co.jp/blog/tech/10282/)
