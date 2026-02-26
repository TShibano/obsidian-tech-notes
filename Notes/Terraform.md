---
title: "Terraform"
date: 2026-02-26
tags:
  - DevOps
  - IaC
  - Cloud
related:
  - "[[データ基盤]]"
  - "[[Kubernetes]]"
  - "[[MLOps]]"
---

## 概要

TerraformはHashiCorpが開発するオープンソースのInfrastructure as Code（IaC）ツール。HCL（HashiCorp Configuration Language）という宣言型言語でインフラの状態を定義し、クラウドプロバイダーの API を通じてリソースの作成・変更・削除を管理する。

## 詳細

### 基本概念

インフラの「あるべき姿」を `.tf` ファイルに記述し、`terraform apply` で実際のインフラに反映する宣言型アプローチを採用。手続き的なスクリプトと異なり、差分を自動計算して必要な変更のみ実行する。

### アーキテクチャ

- **Provider**: Terraform がクラウドプラットフォーム（AWS, GCP, Azure, Kubernetes等）と通信するためのプラグイン。1,000以上が公開されており、GitHub や Datadog なども管理可能
- **State（状態ファイル）**: 実際のインフラとコードの対応関係を記録する `terraform.tfstate`。チームでの利用時はS3+DynamoDB等のリモートバックエンドで管理する
- **Module**: 再利用可能なリソースのまとまり。モジュール化によりDRYなインフラコードを実現
- **Plan / Apply**: `terraform plan` で差分を確認し、`terraform apply` で適用する2段階フロー

### 主なコマンド

```bash
terraform init     # プロバイダーのダウンロード
terraform plan     # 実行計画の確認（変更なし）
terraform apply    # インフラへの適用
terraform destroy  # リソースの削除
terraform fmt      # コードフォーマット
```

### HCL の例

```hcl
resource "aws_s3_bucket" "data_lake" {
  bucket = "my-data-lake"
  tags = {
    Environment = "production"
    Team        = "data-engineering"
  }
}
```

### 市場シェアと現状（2025〜2026）

IaC ツール市場で34.28%のシェアを持ち、マルチクラウド・ハイブリッドクラウド環境でのデファクトスタンダード。2023年にライセンスが BSL（Business Source License）に変更され、OSSフォークとして **OpenTofu** が誕生した。

## ポイント

- **宣言型**: 「何をするか」ではなく「どうあるべきか」を記述する
- **べき等性**: 何度 apply しても同じ結果になる
- **シークレット管理**: 秘密情報を `.tf` に平文で書かない。HashiCorp Vault や環境変数を使う
- **Remote State**: チーム開発では状態ファイルをリモート（S3等）で共有しロック管理する
- **モジュール化**: 再利用性のある小さなモジュールに分割し、単一巨大スクリプトを避ける
- **linting / security scan**: `tflint`, `checkov`, `tfsec` でコード品質とセキュリティを自動チェックする

## 関連項目

- [[データ基盤]] — データ基盤のクラウドリソース管理に Terraform が使われる
- [[Kubernetes]] — Kubernetes クラスタのプロビジョニングも Terraform で管理可能
- [[MLOps]] — ML 実行環境のインフラ管理に活用される

## 参考

- [HashiCorp Developer: What is Terraform?](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/infrastructure-as-code)
- [Spacelift: Terraform IaC Guide](https://spacelift.io/blog/terraform-infrastructure-as-code)
