---
title: "Ceph"
date: 2026-08-02
tags:
  - Cloud
  - DB
related:
  - "[[オブジェクトストレージ]]"
  - "[[ブロックストレージ]]"
  - "[[MinIO]]"
---

## 概要

Ceph は，オブジェクト・ブロック・ファイルという3種類のストレージインターフェースを単一の分散クラスタで統一的に提供するオープンソースストレージシステム．中核には RADOS（Reliable Autonomic Distributed Object Store）と呼ばれる分散オブジェクトストアがあり，その上に RGW（[[オブジェクトストレージ]]），RBD（[[ブロックストレージ]]），CephFS（POSIX ファイルシステム）が構築されている．

## 詳細

### RADOS: すべての基盤

Ceph の上位抽象（RBD のブロックデバイス，CephFS のファイルシステム，RGW のオブジェクトストレージ）は，最終的にすべて RADOS 上のオブジェクトとして保存される．RADOS は中央集権的なメタデータサーバーに依存せず，CRUSH アルゴリズム（一貫性ハッシュの一種）・分散クラスタマップ・OSD の自律的な協調動作によって，スケーラブルで信頼性の高いストレージを実現する．

### 主要コンポーネント

| コンポーネント | 役割 |
|--------------|------|
| **Monitor（MON）** | クラスタマップ（CRUSH map，OSD map，PG map 等）の正本を維持．クライアントはまず MON に問い合わせてデータの所在を特定する |
| **OSD（Object Storage Daemon）** | 物理メディア上にオブジェクトを実際に格納．レプリケーション，リカバリ，バックフィル，ハートビート監視も担う |
| **Manager（MGR）** | メトリクス収集，ダッシュボードや Prometheus エクスポーター等のモジュールをホスト |
| **RGW（RADOS Gateway）** | S3 / Swift 互換の REST API を提供するオブジェクトストレージゲートウェイ |
| **RBD（RADOS Block Device）** | 仮想ブロックデバイスを提供．OpenStack や Kubernetes（Rook 経由）で広く利用 |
| **CephFS** | POSIX 準拠の分散ファイルシステム |
| **LIBRADOS** | アプリケーションが RADOS に直接アクセスするためのライブラリ |

### スケーラビリティの仕組み

RADOS の設計上の特徴は，OSD 群が中央コーディネータへの依存を最小化し，障害からの復旧やクラスタ拡張に伴うデータ移行を自律的に処理できる点にある．CRUSH アルゴリズムにより，クライアントは中央のルックアップテーブルを介さずにオブジェクトの配置先を計算でき，中央メタデータサーバーがボトルネックにならない．

### オブジェクトストレージとしての Ceph（RGW）

RGW は S3 / Swift 互換 API を提供し，[[オブジェクトストレージ]]として利用できる．OpenStack エコシステムとの統合が強みで，エンタープライズ・プライベートクラウド環境で広く採用されている．[[MinIO]] や [[Garage]] と比べて，ブロック・ファイル・オブジェクトを1つのクラスタで統合的に運用できる点が特徴だが，その分アーキテクチャは複雑で運用の学習コストは高め．

## ポイント

- RADOS を共通基盤として，オブジェクト（RGW）・ブロック（RBD）・ファイル（CephFS）の3種類のストレージを統一クラスタで提供
- CRUSH アルゴリズムと OSD の自律的協調により，中央メタデータサーバー無しでスケーラビリティと信頼性を両立
- OpenStack / Kubernetes（Rook）との統合が強く，エンタープライズ・プライベートクラウドで広く利用
- MinIO・Garage 等の「オブジェクトストレージ専用」ツールに比べて機能は広いが，運用の複雑さもその分大きい

## 関連項目

- [[オブジェクトストレージ]] — Ceph RGW が提供するインターフェースの一つ
- [[ブロックストレージ]] — Ceph RBD が提供するインターフェースの一つ
- [[MinIO]] — より軽量でオブジェクトストレージに特化した代替ツール

## 参考

- [Ceph.io — The RADOS distributed object store](https://ceph.io/en/news/blog/2009/the-rados-distributed-object-store/)
- [Ceph.io — Ceph Object Storage Deep Dive Series. Part 1](https://ceph.io/en/news/blog/2025/rgw-deep-dive-1/)
- [Red Hat Ceph Storage - The Ceph Object Gateway](https://docs.redhat.com/en/documentation/red_hat_ceph_storage/5/html/object_gateway_guide/the-ceph-object-gateway_rgw)
