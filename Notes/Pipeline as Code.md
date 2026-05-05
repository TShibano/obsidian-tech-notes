---
title: "Pipeline as Code"
date: 2026-05-06
tags:
  - DevOps
  - CI/CD
related:
  - "[[IaC]]"
  - "[[DataOps]]"
  - "[[Apache Airflow]]"
  - "[[Dagster]]"
  - "[[Prefect]]"
---

## 概要

Pipeline as Code とは，CI/CD パイプラインの構成をコードファイルとして定義・バージョン管理する手法．UI 上での手動設定ではなく，Jenkinsfile や YAML ファイルとしてアプリケーションコードと同一リポジトリで管理する．

## 詳細

従来の CI/CD ツールはブラウザ UI でジョブを設定するため，設定とコードが分離していた．Pipeline as Code ではパイプライン定義をソースコントロールに含めることで，コードレビュー・差分確認・ロールバックをパイプライン構成にも適用できる．

### 主要ツール

| ツール | 定義ファイル | 特徴 |
|--------|------------|------|
| Jenkins | Jenkinsfile (Groovy) | 先駆け，豊富なプラグイン |
| GitHub Actions | `.github/workflows/*.yml` | GitHub 統合，1日6,000万以上のジョブを処理 |
| GitLab CI | `.gitlab-ci.yml` | GitLab 統合，自己ホスト可能 |
| CircleCI | `.circleci/config.yml` | Docker ネイティブ，セットアップが容易 |

### データパイプラインの文脈

[[Apache Airflow]]・[[Dagster]]・[[Prefect]] などのワークフローオーケストレーターも「パイプラインをコードで定義する」思想であり，広義の Pipeline as Code に含まれる．

### GitHub Actions の現状（2025-2026）

- 毎日6,000万以上のワークフローが実行
- 2025年8月のアーキテクチャ刷新でスケジューリング容量が3倍に
- Jenkins・CircleCI・Travis CI からの自動移行 CLI（Importer）を公式提供

## ポイント

- パイプライン定義をコードとして管理することで変更の透明性と再現性が向上する
- アプリケーションコードと同一 PR でパイプライン変更をレビューできる
- [[IaC]] と組み合わせることでインフラからアプリデプロイまで全自動化できる
- セルフドキュメンテーション：パイプラインの定義がそのまま仕様書となる

## 関連項目

- [[IaC]]
- [[DataOps]]
- [[Apache Airflow]]
- [[Dagster]]
- [[Prefect]]

## 参考

- [Pipeline as Code with Jenkins](https://www.jenkins.io/solutions/pipeline/)
- [Pipeline as Code | Jenkins Documentation](https://www.jenkins.io/doc/book/pipeline/pipeline-as-code/)
- [GitHub Actions CI/CD Pipeline Setup Guide | getint.io](https://www.getint.io/blog/github-actions-cicd-pipeline-setup)
