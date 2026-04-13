---
title: "Docker"
date: 2026-02-26
tags:
  - DevOps
  - Tool
  - Cloud
related:
  - "[[Kubernetes]]"
  - "[[データ基盤]]"
  - "[[MLOps]]"
  - "[[Apache Airflow]]"
  - "[[Apache Kafka]]"
  - "[[podman]]"
---

## 概要

Dockerはアプリケーションとその依存環境を「コンテナ」としてパッケージ化し、どこでも同じ環境で動かすためのオープンソースプラットフォーム。2013年のリリース以降、開発・テスト・本番環境の一貫性を担保する手段として広く普及し、クラウドネイティブ開発の基盤技術となっている。

## 詳細

### コアコンセプト

- **Image（イメージ）**: アプリケーション実行に必要なコード・ライブラリ・設定をまとめた不変のテンプレート。Dockerfile から `docker build` で生成する
- **Container（コンテナ）**: Imageの実行インスタンス。ホストOSのカーネルを共有しつつ、ファイルシステム・プロセス・ネットワークが分離された軽量な実行環境
- **Dockerfile**: Imageの構築手順を記述するテキストファイル
- **Registry**: Imageを保存・共有するリポジトリ。Docker Hub（公式）や AWS ECR, GCR 等がある

### VM との違い

| 項目 | VM（仮想マシン） | コンテナ |
|------|-----------------|---------|
| サイズ | GB 単位 | MB 単位 |
| 起動時間 | 数分 | 秒以下 |
| OSカーネル | 独立したゲストOS | ホストOSを共有 |
| 分離性 | 強い | 中程度 |
| ポータビリティ | 低い | 高い |

### Dockerfile の例

```dockerfile
# データエンジニアリング用 Python 環境
FROM python:3.12-slim

WORKDIR /app

# 依存パッケージのインストール
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# アプリケーションコードをコピー
COPY . .

# 実行コマンド
CMD ["python", "pipeline.py"]
```

### Docker Compose

複数のコンテナを定義・起動するためのツール。ローカル開発環境でKafka + Spark + Airflow などを一括起動する用途で多用される。

```yaml
# docker-compose.yml
services:
  kafka:
    image: confluentinc/cp-kafka:latest
    ports:
      - "9092:9092"

  spark:
    image: bitnami/spark:latest
    environment:
      - SPARK_MODE=master

  airflow:
    image: apache/airflow:2.9.0
    ports:
      - "8080:8080"
    depends_on:
      - kafka
```

### データエンジニアリングでの活用

| ユースケース | 説明 |
|-------------|------|
| **ローカル開発環境の統一** | Spark, Kafka, Airflow をローカルで Docker Compose で起動 |
| **パイプラインの再現性** | ETL ジョブをコンテナ化し、どの環境でも同一動作を保証 |
| **本番デプロイ** | Kubernetes 上で Docker コンテナとして本番データパイプラインを実行 |
| **ML モデルサービング** | モデルと依存ライブラリをコンテナにまとめて配布 |
| **依存関係の分離** | 異なる Python バージョン・ライブラリが必要な複数パイプラインを分離 |

### 主要コマンド

```bash
docker build -t my-pipeline:v1 .     # Image のビルド
docker run my-pipeline:v1            # コンテナの起動
docker run -it ubuntu bash           # インタラクティブ起動
docker ps                            # 実行中コンテナの確認
docker images                        # Image の一覧
docker push my-registry/my-pipeline  # Registry への push
docker-compose up -d                 # Compose で一括起動
docker-compose down                  # 一括停止・削除
```

### Kubernetes との関係

Docker がコンテナを「作る・動かす」ツールであるのに対し、Kubernetes はコンテナを「大規模に管理・オーケストレーション」するツール。ローカル開発では Docker/Compose を使い、本番環境では Kubernetes でスケールさせる構成が一般的。

## ポイント

- **「自分のマシンでは動く」問題の解消**: 依存環境ごとコンテナ化するため、開発・CI・本番で完全に同じ環境が再現できる
- **.dockerignore**: `.gitignore` と同様に不要ファイルを除外し、Image サイズを削減する
- **レイヤーキャッシュ**: Dockerfile の命令はレイヤーとしてキャッシュされる。変更頻度の低い命令を先に書くと再ビルドが速くなる
- **非rootユーザー**: セキュリティのため、コンテナ内では root ではなく専用ユーザーで実行する
- **マルチステージビルド**: ビルド用とランタイム用の Image を分けて、最終 Image サイズを削減する

## 関連項目

- [[Kubernetes]] — Docker コンテナを大規模に管理するオーケストレーター
- [[データ基盤]] — データパイプラインのコンテナ化による再現性・可搬性の確保
- [[MLOps]] — ML モデルのコンテナ化と本番デプロイ
- [[Apache Airflow]] — Airflow 自体や DAG タスクをコンテナで実行
- [[Apache Kafka]] — ローカル開発での Kafka クラスタ起動に Docker Compose を使用

- [[podman]] — デーモンレス・ルートレスを特徴とする Docker 互換のコンテナエンジン

## 参考

- [Docker公式ドキュメント](https://docs.docker.com/)
- [Start Data Engineering: Docker for Data Engineers](https://www.startdataengineering.com/post/docker-for-de/)
