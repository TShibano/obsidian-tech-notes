---
title: "Memcached"
date: 2026-05-10
tags:
  - DB
related:
  - "[[Redis]]"
  - "[[キャッシュ]]"
  - "[[KVストア]]"
---

## 概要

Memcachedは，シンプルなキー・バリュー型のインメモリキャッシュシステム．2003年にBrad Fitzpatrickが開発．高速な読み書きと水平スケールが特徴で，Webアプリケーションのデータベース負荷軽減に広く使われる．

## 詳細

### 仕組み

- 全データをメモリ上に格納し，ディスクへの永続化を行わない
- アイテムはキーと値のペアで保存し，TTL（有効期限）を設定可能
- LRU（Least Recently Used）アルゴリズムでメモリ枯渇時に古いデータを自動削除
- マルチスレッドで動作し，複数コアを効率的に活用

### [[Redis]] との比較

| 比較項目 | Memcached | [[Redis]] |
|---------|-----------|--------|
| データ構造 | 文字列のみ | 文字列・リスト・セット・ハッシュ・ソート済みセット等 |
| 永続化 | なし | RDB / AOF をサポート |
| スレッドモデル | マルチスレッド | シングルスレッド（Redis 6.0+ は部分的にマルチスレッド） |
| クラスタ | クライアント側シャーディング | Redis Cluster / Sentinel |
| Pub/Sub | なし | あり |
| トランザクション | なし | MULTI/EXEC |
| 主な用途 | 単純なキャッシュ | キャッシュ・セッション・キュー・リアルタイム処理 |

### 分散アーキテクチャ

Memcached 自体は分散機能を持たず，クライアント（アプリケーション）側で一貫性ハッシュ等を使いデータを複数ノードに振り分ける．ノード追加・削除時のリバランスはクライアントが担う．

### 主なユースケース

- データベースクエリ結果のキャッシュ（N+1問題の解消）
- セッションデータの分散格納（ステートレスアプリ化）
- APIレスポンスキャッシュ
- 頻繁に参照されるが変化の少ない静的データのキャッシュ

## ポイント

- シンプルさゆえの高速性が最大の強み（設定・運用が容易）
- 永続化が不要で純粋なキャッシュに徹するケースではRedisより低オーバーヘッド
- 再起動やノード障害でデータが消えるため，キャッシュ以外の用途には不向き
- AWS ElastiCache for Memcached としてマネージドサービスが提供されている

## 関連項目

- [[Redis]]
- [[キャッシュ]]
- [[KVストア]]

## 参考

- [Redis 作者による Redis と Memcached の比較 - Yakst](https://yakst.com/ja/posts/3243)
- [Memcached vs Redis - AWS](https://aws.amazon.com/elasticache/redis-vs-memcached/)
- [初心者でもわかる Redis と Memcached の違い - Asia Quest](https://techblog.asia-quest.jp/202410/differences-between-redis-and-memcached-and-their-use-in-web-applications)
