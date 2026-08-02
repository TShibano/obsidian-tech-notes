---
title: "RustFS"
date: 2026-08-02
tags:
  - Cloud
  - DB
related:
  - "[[オブジェクトストレージ]]"
  - "[[MinIO]]"
  - "[[Rust]]"
  - "[[Amazon S3]]"
---

## 概要

RustFS は，[[Rust]]で実装された S3 互換の高性能[[オブジェクトストレージ]]．MinIO の代替として位置づけられ，4KB のような小さいオブジェクトのペイロードで MinIO の 2.3 倍のスループットを謳う．AI ワークロード（ベクトルDB・分析基盤との連携）を主要ユースケースの一つとして掲げ，Apache 2.0 ライセンスで公開されている．

## 詳細

### MinIO との関係と位置づけ

RustFS は MinIO・Ceph 等の既存 S3 互換プラットフォームとの移行・共存をサポートすることを明言しており，MinIO からの乗り換え先として設計されている．MinIO は近年 AGPL ライセンスへの一本化で商用利用の制約が話題になったが，RustFS は Apache 2.0 ライセンスを採用することで，その制約を避けている．

### 技術的特徴

- **Rust による実装**: メモリ安全性を保ちながら高いパフォーマンスを実現
- **S3 互換 API**: AWS Signature Version 4（SigV4）に対応し，AWS CLI や各種 S3 SDK からそのまま利用可能
- **IAM 互換のアクセス制御**: AWS IAM 相当のポリシーベースアクセス制御をサポート
- **マルチ環境対応**: パブリッククラウド，プライベートクラウド，データセンター，マルチクラウド，ハイブリッド，エッジ環境をまたいで動作

### 想定ユースケース

- コードリポジトリのオブジェクトストレージ（GitHub/GitLab のようなワークロード）
- 分析基盤（MongoDB，ClickHouse，MariaDB，CockroachDB，Teradata 等との組み合わせ）
- バックアップ・アーカイブ
- AI/ML 向けの大規模データストレージ（[[ベクトルDB]] のバックエンドとしての評価事例あり，例: Milvus）

### ライセンスとエコシステム

Apache 2.0 ライセンスにより，AGPL のような「派生物の公開義務」を回避できる点が企業導入における訴求点になっている．MinIO や Ceph からのデータ移行機能を備え，既存 S3 互換エコシステムからの乗り換えを容易にしている．

## ポイント

- Rust 製の S3 互換オブジェクトストレージで，MinIO の直接的な代替として設計
- 小オブジェクト（4KB）で MinIO 比 2.3 倍のスループットを謳う
- Apache 2.0 ライセンスにより MinIO の AGPL 制約を回避
- AI/ML ワークロード（ベクトルDB連携等）を意識したポジショニング

## 関連項目

- [[オブジェクトストレージ]] — RustFS が実装するストレージ方式
- [[MinIO]] — RustFS が直接の代替として位置づける既存ツール
- [[Rust]] — RustFS の実装言語
- [[Amazon S3]] — RustFS が互換性を提供する API の標準

## 参考

- [RustFS 公式サイト](https://rustfs.com/)
- [GitHub - rustfs/rustfs](https://github.com/rustfs/rustfs)
- [Amazon S3 Compatibility - RustFS Documentation](https://docs.rustfs.com/features/s3-compatibility/)
