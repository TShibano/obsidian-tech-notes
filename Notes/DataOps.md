---
title: "DataOps"
date: 2026-02-26
tags:
  - DevOps
  - DB
related:
  - "[[データ品質]]"
  - "[[データリネージ]]"
  - "[[Apache Airflow]]"
  - "[[MLOps]]"
  - "[[data version control]]"
  - "[[テスト駆動開発]]"
  - "[[dbt]]"
---

## 概要

DataOps（Data Operations）は、DevOps の原則をデータエンジニアリングとデータ分析のライフサイクルに適用した方法論。自動化・協調・継続的デリバリーを通じて、データパイプラインの品質向上・リードタイム短縮・信頼性確保を目指す。「データパイプラインをコードとして扱う」という思想が核心。

## 詳細

### DataOps の5つのコア原則

1. **継続的デリバリー（CD）**: データパイプラインの変更を頻繁・小刻みにリリースし、リスクを分散する
2. **自動化（Automation）**: テスト・デプロイ・監視を自動化し、人的ミスと手作業コストを削減する
3. **バージョン管理（Version Control）**: パイプラインコード・SQL変換・設定を Git で管理する
4. **品質保証（Quality Assurance）**: データの品質チェックをパイプラインに組み込む
5. **コラボレーション（Collaboration）**: データエンジニア・アナリスト・データサイエンティストが連携する

### DevOps と DataOps の対比

| DevOps | DataOps |
|--------|---------|
| アプリケーションコード | データパイプライン・SQL変換 |
| ユニットテスト / 統合テスト | データ品質チェック / スキーマテスト |
| CI/CD パイプライン | データパイプラインのCI/CD |
| アプリケーションモニタリング | データ品質モニタリング / Consumer Lag |
| Docker / Kubernetes | Airflow / Spark / dbt |

### DataOps のプラクティス

#### CI/CD for データパイプライン

```yaml
# .github/workflows/data-pipeline.yml
on:
  push:
    branches: [main]

jobs:
  test:
    steps:
      - name: dbt テストの実行
        run: dbt test --select staging+

      - name: データ品質チェック
        run: soda scan -d my_warehouse checks.yml

      - name: デプロイ
        if: success()
        run: dbt run --select staging+
```

#### データパイプラインの Git フロー

```
feature/add-orders-model
      ↓（PR + dbt test 自動実行）
develop
      ↓（品質チェック通過後）
main（本番デプロイ）
```

#### テスト戦略

| テスト種別 | ツール | 内容 |
|-----------|--------|------|
| スキーマテスト | dbt tests | not_null, unique, accepted_values |
| 統計的品質チェック | Great Expectations / Soda | 件数の閾値、分布の異常検出 |
| パイプライン統合テスト | Pytest + 本番相当データ | エンドツーエンドの動作確認 |
| リグレッションテスト | dbt + 結果比較 | 変更前後のデータ差分を検出 |

### DataOps のツールスタック

```
オーケストレーション:  Apache Airflow / Prefect / Dagster
変換:                 dbt（SQL変換 + テスト + ドキュメント）
品質検証:             Great Expectations / Soda
バージョン管理:        Git + DVC（data version control）
CI/CD:               GitHub Actions / GitLab CI
インフラ:             Terraform + Docker + Kubernetes
監視:                 Datadog / Grafana + AlertManager
リネージ:             OpenLineage / Marquez
```

### データオブザーバビリティ

DataOps の重要な要素として、パイプラインの「見える化」がある：

- **フレッシュネス（Freshness）**: データが期待通りの頻度で更新されているか
- **ボリューム（Volume）**: レコード件数が通常範囲内か（突然の増減を検出）
- **スキーマ（Schema）**: カラムの追加・削除・型変更を検出
- **ディストリビューション（Distribution）**: 値の分布が通常範囲を逸脱していないか
- **リネージ（Lineage）**: 上流の変化が下流にどう伝播したか

商用ツール: Monte Carlo, Sifflet, Bigeye、OSS: Elementary Data

### 2025年のトレンド: AI-Augmented DataOps

Gartner が指摘する通り、AI を組み込んだ DataOps プラットフォームが台頭：
- 異常検出の自動化（機械学習による動的閾値設定）
- 根本原因分析の自動提案
- パイプライン最適化の自動推薦

## ポイント

- **「シフトレフト」の発想**: データの問題を下流のレポートではなく、パイプラインの上流で検出・修正する
- **小さな変更・頻繁なリリース**: 大きな変更をまとめてリリースすると問題の特定が困難になる。小刻みなリリースが重要
- **データ契約（Data Contracts）**: プロデューサーとコンシューマー間でスキーマ・品質を合意した仕様書。スキーマドリフトを防ぐ
- **効果の指標**: リードタイム（コードコミットからデプロイまで）、障害復旧時間（MTTR）、データ品質エラー率で DataOps の成熟度を測る
- **組織文化の変革**: ツールだけでなく、データを「製品として扱う」文化の変革が不可欠

## 関連項目

- [[データ品質]] — DataOps のコアプラクティスのひとつ
- [[データリネージ]] — パイプラインの観測性を担う要素
- [[Apache Airflow]] — DataOps のオーケストレーション基盤
- [[MLOps]] — DataOps の ML 版。考え方が共通する
- [[data version control]] — データのバージョン管理で Git フローを実現
- [[テスト駆動開発]] — TDD の考え方をデータパイプラインに適用する
- [[dbt]] — SQL変換・テスト・ドキュメントを統合する DataOps の中核ツール

## 参考

- [Acceldata: What Is DataOps?](https://www.acceldata.io/blog/what-is-dataops-principles-benefits-and-best-practices)
- [Ascend: CI/CD for Data Teams](https://www.ascend.io/blog/ci-cd-for-data-teams-a-roadmap-to-reliable-data-pipelines)
