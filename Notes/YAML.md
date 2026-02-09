---
title: "YAML"
date: 2026-02-09
tags:
  - Tool
  - DevOps
related:
  - "[[JSON]]"
  - "[[TOML]]"
  - "[[データフォーマット]]"
---

## 概要

YAML（YAML Ain't Markup Language）は、人間が読み書きしやすいことを重視したデータシリアライゼーション言語である。インデントベースの構造で可読性が高く、Kubernetes のマニフェストや CI/CD パイプラインの設定ファイルなど、DevOps 領域で広く使われている。

## 詳細

### 歴史と仕様

YAML は2001年に Clark Evans、Ingy döt Net、Oren Ben-Kiki によって設計された。名前は当初「Yet Another Markup Language」の略だったが、後に再帰的頭字語「YAML Ain't Markup Language」に変更され、マークアップ言語ではなくデータ指向の言語であることが強調された。

| バージョン | リリース日 | 主な変更点 |
|-----------|-----------|-----------|
| YAML 1.0 | 2004-01-29 | 初期仕様 |
| YAML 1.1 | 2005-01-18 | 機能追加 |
| YAML 1.2 | 2009-07-21 | [[JSON]] の厳密なスーパーセット化、暗黙的型変換の削減 |
| YAML 1.2.2 | 2021-10-01 | エラー修正と明確化（規範的変更なし） |

YAML 1.2 の最大の変更点は、[[JSON]] の厳密なスーパーセットとなるよう設計されたことである。つまり、有効な JSON は常に有効な YAML として解釈できる。

### 基本構文

```yaml
# スカラー値
name: "YAML"
version: 1.2
enabled: true

# リスト
items:
  - item1
  - item2

# ネストしたマッピング
database:
  host: localhost
  port: 5432

# 複数ドキュメント（--- で区切り）
---
document1: value
---
document2: value
```

特徴的な構文要素:

- **インデントベース**: スペースでネスト構造を表現（タブは不可）
- **コメント**: `#` でコメントが記述可能（[[JSON]] にはない利点）
- **複数ドキュメント**: `---` で1ファイルに複数ドキュメントを格納可能
- **アンカーとエイリアス**: `&` と `*` でデータの再利用が可能

### 主な用途

#### Kubernetes マニフェスト

Kubernetes では Pod、Deployment、Service、ConfigMap などすべてのリソースが YAML で定義される。コミュニティでも [[JSON]] より YAML が推奨されており、可読性の高さとコメント機能が選ばれる理由となっている。

#### CI/CD パイプライン

主要な CI/CD ツールが YAML を設定形式として採用している:

- **GitHub Actions**: `.github/workflows/*.yml`
- **GitLab CI**: `.gitlab-ci.yml`
- **CircleCI**: `.circleci/config.yml`
- **Azure Pipelines**: `azure-pipelines.yml`

#### Infrastructure as Code（IaC）

[[Apache Airflow]] の DAG 定義や Ansible のプレイブック、Docker Compose（`docker-compose.yml`）など、インフラ管理ツールでも広く使われている。

#### アプリケーション設定

Spring Boot（`application.yml`）、Ruby on Rails（`database.yml`）など、多くのフレームワークが YAML を設定ファイル形式としてサポートしている。

### セキュリティ上の注意点

YAML にはデシリアライゼーションに関するセキュリティリスクが存在する:

- **任意コード実行**: Python の `yaml.load()` をセーフローダーなしで使うと、任意の Python オブジェクトが実行される脆弱性がある。`yaml.safe_load()` を使用すべき
- **Billion Laughs 攻撃**: エンティティ展開を悪用した DoS 攻撃。XML と同様の手法が YAML にも適用可能
- **SnakeYaml（Java）**: CVE-2022-1471 として報告された脆弱性。SnakeYaml 2.0 で `SafeConstructor` がデフォルト化され対策済み

### YAML vs JSON vs TOML

| 観点 | YAML | [[JSON]] | [[TOML]] |
|------|------|------|------|
| 可読性 | 高い | 中程度 | 高い |
| コメント | 対応 | 非対応 | 対応 |
| データ型 | 豊富（日付等） | 基本6型 | 豊富（日時等） |
| 複雑な構造 | 得意 | 得意 | 深いネストが苦手 |
| パース安全性 | 要注意 | 安全 | 安全 |
| 主な用途 | DevOps、設定 | API、データ交換 | 設定ファイル |

## ポイント

- YAML はインデントベースの可読性が高いデータシリアライゼーション言語
- YAML 1.2 以降は [[JSON]] の厳密なスーパーセット
- Kubernetes、CI/CD、IaC など DevOps 領域で事実上の標準設定ファイル形式
- コメント、アンカー・エイリアス、複数ドキュメントなど [[JSON]] にない機能を持つ
- デシリアライゼーション時のセキュリティリスクに注意が必要（必ずセーフローダーを使用する）
- インデントに敏感であり、スペースの誤りがデプロイ障害の原因になることがある

## 関連項目

- [[JSON]] - YAML のサブセットであるデータ交換フォーマット
- [[TOML]] - 設定ファイルに特化したシンプルなフォーマット
- [[データフォーマット]] - データ形式の体系的な整理
- [[Apache Airflow]] - YAML で DAG 設定を定義可能なワークフローエンジン

## 参考

- [YAML 公式サイト](https://yaml.org/)
- [YAML 1.2.2 仕様](https://yaml.org/spec/1.2.2/)
- [YAML Specification Index](https://yaml.org/spec/)
- [Kubernetes Configuration Good Practices](https://kubernetes.io/blog/2025/11/25/configuration-good-practices/)
