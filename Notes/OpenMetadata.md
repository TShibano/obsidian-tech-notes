---
title: "OpenMetadata"
date: 2026-09-07
tags:
  - DB
  - Cloud
related:
  - "[[データカタログ]]"
  - "[[DataHub]]"
  - "[[Apache Atlas]]"
---

## 概要

OpenMetadata は，[[データカタログ]]・データ発見・データガバナンスのためのオープンソースのメタデータプラットフォーム．Uber の内製ツール Databook の開発チームによって創設され，スキーマファースト（schema-first）設計を軸に，データ資産・リネージ・品質・オーナーシップを1つの統合メタデータグラフで管理する．

## 詳細

### 設計思想

すべてのエンティティ型を JSON スキーマで定義し，API もそのスキーマから自動生成される API-first / schema-first のアーキテクチャを採用している．データ資産・リネージ・品質メトリクス・オーナーシップ・ドキュメントを「統合メタデータグラフ（Unified Metadata Graph）」として一元的にクエリできる点が特徴．

### 主な機能

- **データ発見**: Elasticsearch によるフルテキスト検索で，テーブル名・説明・タグ・会話まで横断的に検索可能
- **ガバナンス**: ロールベースアクセス制御（RBAC）による権限管理
- **データリネージ**: パイプライン・テーブル・ダッシュボード間のデータの流れを追跡
- **データ品質テスト**: データ品質チェックをプラットフォーム上で定義・実行
- **コネクタ**: 120以上のデータソース・ツールに対応

### コミュニティと動向

2026年3月に Linux Foundation に参加し，中立的な財団のガバナンス下に移行した．同年4月には標準仕様 v1.13 をリリース．3,000以上のエンタープライズ導入実績，8,000以上の GitHub スター，370以上のコントリビューターを持つ．

### DataHub との比較

[[DataHub]] と並ぶ代表的なオープンソースのメタデータプラットフォームで，しばしば比較対象となる．OpenMetadata はスキーマファースト設計を強みとする一方，[[DataHub]] は Kafka ベースのリアルタイムストリーミング取り込みを強みとする．[[Apache Atlas]] はより Hadoop エコシステム寄りの older な選択肢として位置づけられる．

## ポイント

- スキーマファースト・API ファーストな設計で，全エンティティが JSON スキーマから生成される
- [[データカタログ]]・ガバナンス（RBAC）・リネージ・データ品質テストを1つのプラットフォームで提供
- 2026年に Linux Foundation 傘下となり，中立的なガバナンス体制に移行
- [[DataHub]] や [[Apache Atlas]] と並ぶオープンソースメタデータプラットフォームの代表格

## 関連項目

- [[データカタログ]] — OpenMetadata が提供する中核機能
- [[DataHub]] — 同じくオープンソースのメタデータプラットフォームで，しばしば比較対象となる
- [[Apache Atlas]] — Hadoop エコシステム発の類似メタデータ管理ツール

## 参考

- [OpenMetadata: Design Principles, Architecture & More | Atlan](https://atlan.com/openmetadata-explained/)
- [Metadata Platforms in 2026: DataHub, OpenMetadata, Atlan, and Catalog Convergence](https://datalakehousehub.com/blog/metadata-platforms-in-2026/)
