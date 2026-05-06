---
title: "bzip2"
date: 2026-05-06
tags:
  - Tool
  - DevOps
related:
  - "[[データフォーマット]]"
  - "[[Apache Parquet]]"
  - "[[gzip]]"
  - "[[Snappy]]"
---

## 概要

bzip2はバロウズ・ホイラー変換（BWT）とハフマン符号化を組み合わせた圧縮ツール・フォーマット．gzipより高い圧縮率を実現する一方，処理速度は遅い．Hadoopなど分散処理環境での大規模データアーカイブに適している．

## 詳細

### 仕様

- アルゴリズム: BWT（バロウズ・ホイラー変換）+ MTF（Move-to-Front）+ ハフマン符号化
- ブロックサイズ: 100〜900KB で処理（`-1` 〜 `-9` で指定）
- 拡張子: `.bz2`

### 特性

| 特性 | 評価 | 詳細 |
|------|------|------|
| 圧縮率 | 高 | gzip より 10〜15% 程度高圧縮 |
| 圧縮速度 | 遅い | gzip の約 3〜10 倍の時間 |
| 解凍速度 | 中程度 | 圧縮より速いが gzip より遅い |
| 分割圧縮 | 可能 | ブロック単位で分割解凍が可能 |
| 並列処理 | pbzip2 で対応 | 並列 bzip2 実装あり |

### 他フォーマットとの比較

| フォーマット | 圧縮率 | 圧縮速度 | 解凍速度 |
|------------|--------|--------|--------|
| gzip | 中 | 速い | 速い |
| bzip2 | 高 | 遅い | 中程度 |
| Snappy | 低 | 非常に速い | 非常に速い |
| zstd | 高 | 非常に速い | 非常に速い |

### Hadoopとの親和性

bzip2 は **splittable**（分割可能）な圧縮形式のため，Hadoop での MapReduce 処理と相性が良い:
- gzip は分割できないため，Hadoop でのブロック並列処理が困難
- bzip2 のブロック構造は HDFS ブロックをまたいで分割・並列処理できる

## ポイント

- 速度より圧縮率を優先するアーカイブ用途に向く
- 現代的なユースケースでは zstd（Zstandard）が速度・圧縮率ともに優れるため，新規採用では zstd が推奨されることが多い
- Parquet の圧縮コーデックとしては Snappy・gzip が主流で bzip2 の採用は少ない

## 関連項目

- [[データフォーマット]]
- [[Apache Parquet]]
- [[gzip]]
- [[Snappy]]

## 参考

- [bzip2 - Wikipedia](https://en.wikipedia.org/wiki/Bzip2)
- [圧縮と解凍 | テキストファイルの圧縮によく用いられる gzip と bzip2 について](https://bi.biopapyrus.jp/os/linux/compress.html)
- [圧縮ツールの比較: gzip, bzip2, lzop, lz4, xz, zstd](https://zenn.dev/masaki_wk/articles/20240406-compressors-comparison)
