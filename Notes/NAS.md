---
title: "NAS"
date: 2026-05-07
tags:
  - Cloud
related:
  - "[[RAID]]"
  - "[[ブロックストレージ]]"
  - "[[オブジェクトストレージ]]"
  - "[[HDD]]"
  - "[[SSD]]"
  - "[[HDFS]]"
---

## 概要

NAS（Network Attached Storage）は，ネットワーク経由でファイル共有を提供するストレージアプライアンス．NFS（UNIX 系）または SMB/CIFS（Windows 系）プロトコルでアクセスし，複数クライアントが同一ファイルシステムを共有できる．

## 詳細

### NAS の構成

```
クライアント PC / サーバ
  ↕ NFS / SMB（TCP/IP ネットワーク）
NAS デバイス（独自 OS + ファイルシステム）
  ↕
HDD / SSD（通常 RAID 構成）
```

### ストレージタイプの比較

| 観点 | NAS | SAN（ブロックストレージ） | [[オブジェクトストレージ]] |
|------|-----|-----------------|---------------------|
| プロトコル | NFS, SMB | iSCSI, FC | HTTP REST |
| アクセス単位 | ファイル | ブロック | オブジェクト |
| 複数クライアント | ネイティブ対応 | 特殊構成が必要 | ネイティブ対応 |
| スケール | 中規模 | 高性能 | 大規模 |
| 用途 | ファイル共有, バックアップ | DB, VM | クラウドデータレイク |

### 主な NAS 製品・サービス

| 製品/サービス | 種別 | 特徴 |
|------------|------|------|
| Synology / QNAP | オンプレ NAS | 家庭〜中小企業向け，豊富な機能 |
| NetApp ONTAP | エンタープライズ | 高機能，重複排除・スナップショット |
| Amazon EFS | クラウド NFS | AWS で POSIX 準拠の共有ファイルシステム |
| Azure Files | クラウド SMB/NFS | Windows 統合しやすい |
| Google Filestore | クラウド NFS | GCP 上の NFS |

### RAID との組み合わせ

NAS デバイスは内部的に [[RAID]] を使って冗長化するのが標準：
- 家庭用: RAID 1（2 台ミラー）
- 中小企業: RAID 5（3〜5 台）
- エンタープライズ: RAID 6 または RAID 10

## ポイント

- 複数サーバが同じファイルシステムをマウントする用途（ML の共有データセット等）に有効
- クラウドでは EFS / Azure Files が NAS の役割を担う
- [[HDFS]] は大規模分散処理向けの独自分散ファイルシステムで，NAS とは設計思想が異なる

## 関連項目

- [[RAID]]
- [[ブロックストレージ]]
- [[オブジェクトストレージ]]
- [[HDD]]
- [[SSD]]
- [[HDFS]]

## 参考

- [Network-attached storage - Wikipedia](https://en.wikipedia.org/wiki/Network-attached_storage)
