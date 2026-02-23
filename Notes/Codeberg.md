---
title: "Codeberg"
date: 2026-02-23
tags:
  - Tool
  - Git
  - DevOps
related:
  - "[[Zig]]"
  - "[[jujutsu]]"
  - "[[git-delta]]"
---

## 概要

Codeberg は、ベルリン拠点の非営利団体 Codeberg e.V. が運営するオープンソース向けの Git ホスティングサービス。Forgejo（Gitea の hard-fork）をベースとし、プライバシーと自由ソフトウェアに特化した GitHub の代替プラットフォームとして、Zig・Gentoo Linux などが 2025〜2026 年にかけて移行している。

## 詳細

### 基本情報

| 項目 | 内容 |
|------|------|
| 運営 | Codeberg e.V.（ドイツ非営利法人） |
| 設立 | 2018年9月（サービス開始: 2019年1月） |
| ベース | Forgejo（Gitea の hard-fork） |
| 資金調達 | 寄付ベース（広告なし） |
| 公式サイト | codeberg.org |
| 規模（2025年11月時点） | 300,000 以上のリポジトリ |

### 主要サービス

| サービス | 説明 |
|---------|------|
| **Git ホスティング** | Forgejo ベースのリポジトリ管理（PR・Issue・Wiki） |
| **Codeberg Pages** | 静的サイトホスティング（GitHub Pages 相当） |
| **Woodpecker CI** | CI/CD パイプライン |
| **Forgejo Actions** | GitHub Actions 互換のワークフロー自動化 |
| **Weblate** | コミュニティ翻訳プラットフォーム |

### Forgejo との関係

Codeberg は Forgejo の最大インスタンスであり、プロジェクトの主要スポンサー組織でもある。

```
Gitea（元祖） → Forgejo（hard-fork、2022年〜） → Codeberg（Forgejo の最大インスタンス）
```

Forgejo は Gitea が企業主導に移行したことへの反発から、コミュニティ主導の fork として誕生した。

### 主要プロジェクトの移行事例

#### Zig プロジェクト（2025年11月）

2025年11月26日、Zig プログラミング言語の公式リポジトリが GitHub から Codeberg へ移行。

- 旧: `github.com/ziglang/zig`（読み取り専用ミラーとして残存）
- 新: `codeberg.org/ziglang/zig`（正式な canonical origin）

#### Gentoo Linux（2026年2月〜）

Gentoo Linux が GitHub からの段階的移行を開始。AI によるデータスクレイピングへの懸念が移行理由。

```
codeberg.org/gentoo/gentoo
```

### CI/CD

**Woodpecker CI** はシンプルかつ拡張性の高い CI/CD エンジンで、Codeberg の主要 CI として採用されている。設定ファイルは `.woodpecker.yaml`。

```yaml
# .woodpecker.yaml の例
steps:
  - name: test
    image: golang:1.23
    commands:
      - go test ./...
  - name: build
    image: golang:1.23
    commands:
      - go build -o app .
```

**Forgejo Actions** は GitHub Actions と互換性を持つため、`.github/workflows/*.yaml` を `.forgejo/workflows/*.yaml` に移動するだけで既存ワークフローを再利用できる。

### Codeberg vs GitHub vs GitLab

| 観点 | Codeberg | GitHub | GitLab |
|------|---------|--------|--------|
| 運営形態 | 非営利法人 | Microsoft（営利） | 上場企業（営利） |
| 資金 | 寄付 | サブスク + 広告 | サブスク |
| 自由ソフトウェア重視 | ◎（最重視） | △ | ○ |
| 機能の豊富さ | △（必要十分） | ◎ | ◎ |
| CI/CD | Woodpecker CI / Forgejo Actions | GitHub Actions | GitLab CI |
| セルフホスト可 | × | × | ○（Forgejo をセルフホスト） |
| プライバシー | ◎（EU 法準拠）| △ | △ |

### なぜ Codeberg が注目されるか

1. **AI スクレイピング問題**: Microsoft/GitHub が AI 学習目的でコードを使用することへの反発
2. **非営利・中立性**: 企業の商業的利益に左右されない運営
3. **EU 法準拠**: GDPR を尊重した欧州拠点の運営
4. **自由ソフトウェアの哲学**: OSS コミュニティの価値観に合致

## ポイント

- GitHub 依存からの脱却を目指すコミュニティが選ぶ非営利 Git ホスティング
- Forgejo ベースで、Gitea との互換性を保ちつつコミュニティ主導で開発
- Zig・Gentoo など著名プロジェクトが移行を開始
- Woodpecker CI + Forgejo Actions で CI/CD も完結できる
- 寄付で運営される非営利組織のため、広告・データ販売なし

## 関連項目

- [[Zig]] - 2025年11月に Codeberg へ移行したプログラミング言語プロジェクト
- [[jujutsu]] - 同じく Git 関連ツール
- [[git-delta]] - Git 差分表示ツール（git エコシステム）

## 参考

- [Codeberg 公式サイト](https://codeberg.org/)
- [What is Codeberg? - Codeberg Documentation](https://docs.codeberg.org/getting-started/what-is-codeberg/)
- [Forgejo 公式サイト](https://forgejo.org/)
- [Zig's Move to Codeberg - Terabyte Systems](https://terabyte.systems/posts/zigs-bold-move-migrating-from-github-to-codeberg/)
- [Gentoo Codeberg Migration](https://biggo.com/news/202602171321_gentoo-linux-moves-from-github-to-codeberg-over-ai-concerns)
