---
title: "Arm"
date: 2026-04-18
tags:
  - AI
  - ML
related:
  - "[[RISC-V]]"
  - "[[AI専用チップ]]"
---

## 概要

Arm（旧称 ARM: Advanced RISC Machine）は、Arm Holdings が設計するプロプライエタリの RISC ベースプロセッサアーキテクチャ。Arm Holdings 自身はチップを製造せず、IP（設計資産）をライセンスするビジネスモデルを採る。スマートフォンのほぼ100%、タブレット・ウェアラブルの大半で採用され、近年は PC・サーバー・HPC 市場にも急速に拡大している。

## 詳細

### 歴史

- **1983年**: Acorn Computers が ARM プロジェクトを開始
- **1990年**: Apple・VLSI Technology の出資で Advanced RISC Machines Ltd.（ARM）として独立
- **1998年**: ロンドン証券取引所・NASDAQ に上場
- **2000年代**: モバイル市場の爆発的成長で世界最多出荷の CPU アーキテクチャに
- **2016年**: ソフトバンクグループが約3.3兆円で買収
- **2020年**: NVIDIA が買収を試みるも規制当局の反対で2022年に断念
- **2023年**: NASDAQ に再上場（ソフトバンクが筆頭株主として維持）

### ビジネスモデル

Arm Holdings は「ファブレス」ではなく「IP ライセンス企業」:

- **ライセンス料**: CPU コア設計（Cortex シリーズ等）の使用権を販売
- **ロイヤリティ**: ライセンシーがチップを出荷するたびに売上の一定割合を徴収
- **アーキテクチャライセンス**: ISA のみをライセンスし、独自コア設計を許可（Apple・Qualcomm が利用）

この IP ライセンスモデルにより、Arm は自社で製造設備を持たずに世界中の半導体企業から収益を得ている。

### プロセッサファミリ

#### Cortex-A（Application）
スマートフォン・タブレット・PC 向けの高性能コア。

| 世代 | 代表コア | 特徴 |
|------|---------|------|
| ARMv8 | Cortex-A72/A76 | 64bit 対応、モバイル全盛期 |
| ARMv9 | Cortex-A720/A725 | SVE2（ベクトル演算）、MTE（メモリタグ）|
| ARMv9.2 | Cortex-X4/X5 | 最高性能コア、big.LITTLE の「big」側 |

#### Cortex-R（Real-time）
自動車・産業用途のリアルタイム処理向け。高い信頼性と決定論的な応答時間を提供。

#### Cortex-M（Microcontroller）
IoT・組み込み向けの超低消費電力コア。Cortex-M0+（最小）〜 Cortex-M85（AI 対応）。

#### Neoverse（Server / Infrastructure）
データセンター・クラウド・HPC 向け:

| シリーズ | 用途 | 採用例 |
|---------|------|--------|
| Neoverse N（効率重視） | クラウドネイティブ | AWS Graviton3/4、Google Axion |
| Neoverse V（性能重視） | HPC・AI 推論 | NVIDIA Grace、Microsoft Cobalt |
| Neoverse E（エッジ） | ネットワーク・エッジ | 5G 基地局 |

### 主要な採用事例

#### Apple Silicon
Apple は Arm アーキテクチャライセンスに基づき独自コアを設計:

- **M シリーズ**（Mac 向け）: M1（2020）→ M2 → M3 → M4（2024）→ M5（2025）
- **A シリーズ**（iPhone 向け）: A17 Pro → A18 Pro
- Intel から Arm への完全移行により、Mac の電力効率と性能を大幅に向上

#### AWS Graviton
Amazon が自社設計した Arm サーバープロセッサ:

- **Graviton3/4**: Neoverse ベース、x86 インスタンス比で最大40%のコスト削減
- クラウドワークロードでの Arm 普及を牽引

#### Qualcomm Snapdragon
- **Snapdragon X Elite / X2 Elite**（PC 向け）: Windows on Arm の中核。NPU 80 TOPS
- **Snapdragon 8 Gen 3**（モバイル向け）: Android フラッグシップ向け

### ARMv9 アーキテクチャ

2021年に発表された最新世代アーキテクチャ:

- **SVE2（Scalable Vector Extension 2）**: 可変長ベクトル演算。AI・ML・DSP ワークロードの高速化
- **MTE（Memory Tagging Extension）**: メモリ安全性の強化。バッファオーバーフロー等の脆弱性を防止
- **CCA（Confidential Compute Architecture）**: ハードウェアレベルの機密コンピューティング
- **Realm Management Extension**: 実行環境の隔離によるセキュリティ強化

### サーバー市場での躍進

Arm ベースサーバーが x86 の牙城に挑んでいる:

- **2025年時点**: クラウドサーバーの約15%が Arm ベース（急成長中）
- **AWS Graviton**: Amazon の新規ワークロードの過半数が Graviton で稼働
- **Google Axion**: Neoverse N3 ベースの自社設計チップ。N4A VM として提供
- **Microsoft Cobalt**: Azure 向け Arm サーバー CPU
- **NVIDIA Grace**: Neoverse V2 ベース。Grace Hopper Superchip として GPU と統合

### PC 市場への進出

- **Apple Mac**: 2020年から Arm 移行完了。PC 市場全体の約13%（2025年）
- **Windows on Arm**: Qualcomm Snapdragon X Elite により本格化。Copilot+ PC 認証
- **Arm ベース PC 市場シェア**: 2025年の約13%から2026年末に30%へ拡大見込み（Canalys 予測）

### 最新動向（2025〜2026年）

- **Arm AGI CPU**: Meta の要請で Arm が完成品 CPU を直接供給。Neoverse V3 ベースの AI ワークロード特化チップ
- **NVIDIA NVLink Fusion 統合**: Arm Neoverse に NVLink を統合し、CPU-GPU 間の高帯域コヒーレント接続を実現
- **Snapdragon X2 Elite Extreme**: Qualcomm の次世代 PC 向け SoC。Apple M4 Pro を上回る CPU 性能（2026年前半出荷）

### RISC-V との比較

| 観点 | Arm | [[RISC-V]] |
|------|-----|-----------|
| エコシステム | 非常に成熟 | 急成長中 |
| ライセンス | 有償（ロイヤリティあり） | 無償 |
| 性能実績 | Apple Silicon・Graviton で実証 | ハイエンドは開発中 |
| カスタマイズ | アーキテクチャライセンスで可能 | 完全に自由 |
| 市場シェア | モバイル支配的、サーバー急成長 | IoT・組み込み中心 |

NVIDIA が Arm と RISC-V 双方に投資している点は注目に値する。

## ポイント

- IP ライセンスモデルにより、自社製造なしで世界中の半導体企業から収益を得る独特のビジネスモデル
- モバイル市場ではほぼ100%のシェアを持ち、PC・サーバーにも急速に拡大中
- Apple Silicon の成功が Arm の PC・サーバー市場進出を加速した
- ARMv9 で SVE2（ベクトル演算）・MTE（メモリ安全性）・CCA（機密計算）を導入
- Arm ベース PC は2026年末に市場シェア30%に到達する見込み

## 関連項目

- [[RISC-V]] - オープンソースの競合 ISA。ライセンスフリーで IoT・AI 特化チップに強み
- [[AI専用チップ]] - Arm Neoverse ベースの AI アクセラレータ（NVIDIA Grace 等）

## 参考

- [Arm 公式: Arm の歴史](https://www.arm.com/ja/resources/blueprint/arm-official-history)
- [ARMアーキテクチャ - Wikipedia](https://ja.wikipedia.org/wiki/ARM%E3%82%A2%E3%83%BC%E3%82%AD%E3%83%86%E3%82%AF%E3%83%81%E3%83%A3)
- [What are Arm-based processors? | Google Cloud](https://cloud.google.com/discover/what-are-arm-based-processors)
- [Top Arm-based innovations from Dec 2025 and Jan 2026 - Arm Newsroom](https://newsroom.arm.com/blog/arm-innovations-from-dec-2025-jan-2026)
- [Arm Comes Full Circle With Homegrown, AI-Tuned Server CPU | Next Platform](https://www.nextplatform.com/compute/2026/03/25/arm-comes-full-circle-with-homegrown-ai-tuned-server-cpu/5211524)
