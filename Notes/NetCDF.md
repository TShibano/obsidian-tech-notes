---
title: "NetCDF"
date: 2026-09-07
tags:
  - DB
  - Cloud
related:
  - "[[データフォーマット]]"
  - "[[Zarr]]"
  - "[[Apache Parquet]]"
---

## 概要

NetCDF（Network Common Data Form）は，配列指向の科学データを保存・共有するための自己記述的な[[データフォーマット]]およびソフトウェアライブラリ群．気候科学・海洋学・気象学など，多次元の格子データを扱う分野で事実上の標準として広く使われている．

## 詳細

### 特徴

- **自己記述性（self-describing）**: ファイル自体に変数名・単位・次元・メタデータが埋め込まれており，データの意味を読み手が別途調べる必要がない
- **可搬性**: 整数・文字・浮動小数点数の内部表現が異なる計算機間でも透過的にアクセスできる
- **ダイレクトアクセス**: 巨大なデータセットの一部分だけを，先頭から順に読み込むことなく効率的に取得できる
- **多次元配列**: 緯度・経度・高度・時間といった複数次元を持つ格子データ（グリッドデータ）の表現に適している

### バージョンと HDF5 との関係

NetCDF-4 以降は内部ストレージ層に [HDF5](https://www.hdfgroup.org/solutions/hdf5/) を採用しており，より大きなファイルサイズ，複数の無制限次元（unlimited dimension），圧縮・チャンク化などの恩恵を受けられるようになった．NetCDF Classic / 64-bit Offset 形式は独自バイナリ形式で，HDF5 ベースの NetCDF-4 とは互換性のレイヤーで接続されている．

### 標準化

OGC（Open Geospatial Consortium）の CF-netCDF（Climate and Forecast conventions）は，時空間で変化する地理空間データをエンコードするための命名・メタデータ規約群であり，NetCDF を地球科学分野の共通言語にしている．Copernicus Marine や NASA Earthdata など，多くの公的データ配信基盤が NetCDF をデフォルト形式として採用している．

### クラウド時代の課題と Zarr

NetCDF はファイル全体を1つの単位として扱う設計が中心のため，クラウドオブジェクトストレージ上での並列読み書きやチャンク単位のアクセスには不向きな面がある．この課題に対応するため，同じ多次元配列データを扱いつつクラウドネイティブな設計を持つ [[Zarr]] フォーマットが近年台頭している．

## ポイント

- 気候・海洋・気象分野で事実上の標準となっている，自己記述的な多次元配列[[データフォーマット]]
- NetCDF-4 は内部的に HDF5 ストレージ層を利用し，圧縮・チャンク化・巨大ファイル対応を実現
- CF-netCDF 規約によりメタデータの相互運用性が確保されている
- クラウドネイティブなワークロードでは [[Zarr]] が代替・補完的な選択肢として使われることが増えている

## 関連項目

- [[データフォーマット]] — NetCDF が属する科学データ形式の総称
- [[Zarr]] — クラウドストレージ向けに設計された NetCDF の後継的な多次元配列フォーマット
- [[Apache Parquet]] — 表形式データにおける列指向フォーマットとの対比

## 参考

- [NetCDF | NSF Unidata](https://www.unidata.ucar.edu/software/netcdf)
- [OGC NetCDF Standard - Geospatial Data Encoding Multidimensional Data](https://www.ogc.org/standards/netcdf/)
- [Introduction to the NetCDF format | Copernicus Marine Help Center](https://help.marine.copernicus.eu/en/articles/9221210-introduction-to-the-netcdf-format)
