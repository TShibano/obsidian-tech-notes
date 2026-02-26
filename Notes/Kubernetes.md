---
title: "Kubernetes"
date: 2026-02-26
tags:
  - Cloud
  - K8s
  - DevOps
related:
  - "[[Docker]]"
  - "[[Terraform]]"
  - "[[データ基盤]]"
  - "[[MLOps]]"
---

## 概要

Kubernetes（K8s）はGoogleが開発しCNCF（Cloud Native Computing Foundation）に寄贈したオープンソースのコンテナオーケストレーションプラットフォーム。コンテナ化されたアプリケーションのデプロイ・スケーリング・管理を自動化する、クラウドネイティブの事実上の標準基盤。

## 詳細

### アーキテクチャ

Kubernetes クラスタは**コントロールプレーン**と**ワーカーノード**で構成される。

#### コントロールプレーン

| コンポーネント | 役割 |
|---------------|------|
| **API Server** | クラスタへの唯一のエントリポイント。`kubectl` やツールがここと通信 |
| **Scheduler** | リソース状況に基づいてワーカーノードにワークロードを割り当てる |
| **Controller Manager** | レプリカ数維持・ノード障害対応などの制御ループを実行 |
| **etcd** | クラスタの状態を保存する分散KVストア |

#### ワーカーノード

| コンポーネント | 役割 |
|---------------|------|
| **kubelet** | コントロールプレーンの指示を受けてコンテナを管理するエージェント |
| **kube-proxy** | ネットワークルーティングを管理 |
| **Container Runtime** | containerd や CRI-O などのコンテナ実行環境 |

### 主要リソース

- **Pod**: コンテナを1つ以上まとめた最小デプロイ単位。同一Pod内はネットワーク/ストレージを共有
- **Deployment**: Podの宣言的管理とローリングアップデートを提供
- **Service**: PodへのネットワークアクセスのためのDNS名と負荷分散を提供
- **ConfigMap / Secret**: 設定情報と秘密情報の外部化
- **PersistentVolume**: Podのライフサイクルから独立した永続ストレージ
- **Namespace**: リソースを論理的にグループ化して分離する

### データエンジニアリングでの活用

- **Apache Spark on K8s**: Spark Driver/Executor を Pod として動的スケール
- **Apache Airflow on K8s**: DAG のタスクを KubernetesPodOperator で実行
- **Apache Kafka on K8s**: StatefulSet による Kafka ブローカーの管理
- **MLflow / Jupyter**: ML実験環境のコンテナ化

### 2026年のトレンド

- **GPU スケジューリング**: AI/ML ワークロード向けの GPU リソース管理が強化
- **in-place スケーリング**: 再起動なしでリソース量を変更可能に
- **eBPF ネットワーキング**: より高性能・観測可能なネットワーク層（Cilium等）
- **AI ワークロードのデファクト基盤**として位置づけが確立

## ポイント

- **宣言型管理**: あるべき状態を YAML で定義し、K8s が実際の状態を合わせ続ける
- **セルフヒーリング**: Podが障害になると自動的に再起動・再スケジュール
- **水平スケーリング**: HPA（Horizontal Pod Autoscaler）でメトリクス連動の自動スケール
- **エコシステム**: Helm（パッケージマネージャ）、ArgoCD（GitOps CD）が標準的に使われる
- **学習コスト**: 概念が多く学習コストは高いが、クラウドネイティブの基礎として必須

## 関連項目

- [[Docker]] — Kubernetes が管理するコンテナを作る基盤技術
- [[Terraform]] — K8s クラスタ自体のプロビジョニングに使われる
- [[データ基盤]] — データ処理ワークロードのオーケストレーション基盤として
- [[MLOps]] — ML パイプラインの実行環境として K8s が活用される

## 参考

- [Kubernetes公式: Production-Grade Container Orchestration](https://kubernetes.io/)
- [DevOpsCube: Kubernetes Architecture Explained](https://devopscube.com/kubernetes-architecture-explained/)
