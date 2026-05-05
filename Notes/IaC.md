---
title: "Infrastructure as Code (IaC)"
date: 2026-05-06
tags:
  - DevOps
  - IaC
related:
  - "[[Terraform]]"
  - "[[Docker]]"
  - "[[Kubernetes]]"
  - "[[Pipeline as Code]]"
  - "[[DataOps]]"
---

## 概要

Infrastructure as Code（IaC）とは，インフラリソースをコードとして定義・管理する手法．バージョン管理・自動化・再現性をインフラにもたらし，クラウド時代のDevOpsの基盤となっている．

## 詳細

IaC 以前のインフラ管理は手動操作やスクリプトに依存しており，環境差異（ドリフト）や再現困難な構成が問題だった．IaC では設定ファイルをソースコードとして扱い，CI/CD パイプラインと統合することで変更管理・レビュー・テストを適用できる．

### アプローチ

| アプローチ | 説明 | 代表ツール |
|-----------|------|-----------|
| 宣言型 | あるべき状態を定義，ツールが差分を解消 | Terraform, Pulumi |
| 命令型 | 実行する操作手順を記述 | Ansible, Chef, Puppet |

### 主要ツール

- **Terraform**: HashiCorp 製のオープンソース IaC ツール．AWS・Azure・GCP 等のマルチクラウドに対応する宣言型ツール．HCL（HashiCorp Configuration Language）でリソースを記述する
- **Ansible**: Red Hat 製の構成管理ツール．YAML（Playbook）で設定を記述し，SSH ベースでエージェントレスに動作する
- **Pulumi**: プログラミング言語（Python・TypeScript・Go 等）で IaC を記述できるツール

### Terraform と Ansible の使い分け

- Terraform: クラウドリソースのプロビジョニング（インフラを作る）
- Ansible: OS・ミドルウェアの構成管理（インフラを設定する）
- 組み合わせることでエンドツーエンドの自動化が実現できる

## ポイント

- コードとして管理することで，変更履歴・レビュー・ロールバックが可能になる
- 同一構成を複数環境（開発・本番）に安全に展開できる
- 手動操作による設定ドリフト（環境差異）を防止できる
- [[Pipeline as Code]] と組み合わせることで CI/CD パイプライン全体を自動化できる

## 関連項目

- [[Terraform]]
- [[Docker]]
- [[Kubernetes]]
- [[Pipeline as Code]]
- [[DataOps]]

## 参考

- [IaC (Infrastructure as Code) とは？をわかりやすく解説 | Red Hat](https://www.redhat.com/en/topics/automation/what-is-infrastructure-as-code-iac)
- [Terraform を用いた IaC 入門 | GeNEE](https://genee.jp/contents/system0006/)
- [Terraform Infrastructure as Code (IaC) Guide | Firefly](https://www.firefly.ai/academy/terraform-iac)
