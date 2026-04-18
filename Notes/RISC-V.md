---
title: "RISC-V"
date: 2026-04-18
tags:
  - AI
  - ML
related:
  - "[[Arm]]"
  - "[[AI専用チップ]]"
---

## 概要

RISC-V（リスクファイブ）は、2010年にカリフォルニア大学バークレー校で開発されたオープンソースの命令セットアーキテクチャ（ISA）。ロイヤリティフリーで誰でも利用・拡張可能であり、組み込みシステムからデータセンターまで幅広い用途に対応する。RISC-V International（スイス拠点の非営利組織）が仕様の標準化・保守を管理している。

## 詳細

### ISA とは

ISA（Instruction Set Architecture）は、ハードウェアとソフトウェアの間のインターフェースを定義する仕様。プロセッサがどの命令を実行できるか、レジスタの構成、メモリアクセス方法などを規定する。代表的な ISA として x86（Intel/AMD）、[[Arm]]、RISC-V がある。

### RISC-V の設計思想

RISC-V は RISC（Reduced Instruction Set Computer）の第5世代として設計された:

- **最小限のベース ISA**: ベース命令セット（RV32I/RV64I）は約50命令のみ。x86 の約1,500命令と比較して極めて簡潔
- **モジュラー拡張**: 必要に応じてオプション拡張を追加する設計
  - M: 乗算・除算
  - A: アトミック操作
  - F/D: 単精度/倍精度浮動小数点
  - V: ベクトル演算
  - C: 圧縮命令（コードサイズ削減）
- **カスタム拡張**: ユーザー独自の命令を追加可能（AI アクセラレーション用命令など）

### オープンソースモデル

RISC-V の最大の特徴はオープンソースライセンス:

| 項目 | RISC-V | [[Arm]] | x86 |
|------|--------|---------|-----|
| ライセンス | オープンソース（無料） | プロプライエタリ（有償） | プロプライエタリ（Intel/AMD のみ） |
| カスタム命令 | 自由に追加可能 | 定義済み拡張のみ | 不可 |
| 設計の自由度 | 完全に自由 | ライセンス範囲内 | なし |
| ロイヤリティ | なし | チップ出荷ごとに発生 | — |

### 主要な採用企業・プロジェクト

| 企業/プロジェクト | 用途 |
|----------------|------|
| SiFive | RISC-V コア IP の最大手。データセンター向け高性能プロセッサ開発 |
| NVIDIA | CUDA の RISC-V 移植、NVLink Fusion 統合 |
| Google | Android 向け RISC-V サポート、社内採用 |
| Qualcomm | ウェアラブル・IoT 向け RISC-V チップ |
| Espressif | ESP32-C3/C6（Wi-Fi/BLE マイコン）に RISC-V コア搭載 |
| Raspberry Pi | RP2350 で RISC-V コアをデュアル搭載 |
| Quintauris | Bosch・BMW・Infineon・NXP・Qualcomm の車載向けジョイントベンチャー（2026年） |

### 適用領域

#### 組み込み・IoT
RISC-V の主力領域。ライセンス費不要のためチップコストを抑えられ、カスタム拡張で用途特化が可能。Espressif の ESP32-C シリーズが代表例。

#### AI / ML アクセラレーション
カスタム命令拡張により、AI 推論に特化した命令セットを設計可能。SiFive は2025年にスカラー・ベクトル・マトリクス演算を統合した新 IP を発表。

#### データセンター
SiFive が NVIDIA NVLink Fusion を統合したデータセンター向けプロセッサを開発中（2026年発表）。RISC-V CPU が NVIDIA GPU と高帯域・コヒーレント接続可能に。

#### 車載
Quintauris（Bosch、BMW、Infineon、NXP、Qualcomm の合弁、2026年）が車載向け RISC-V の標準化を推進。

### 最新動向（2025〜2026年）

- **NVIDIA の戦略的投資**: 2026年4月、SiFive が $4億の Series G を調達（評価額 $36.5億）。NVIDIA が新規戦略投資家として参加。Arm ライセンスへの依存軽減が背景
- **CUDA の RISC-V 移植**: 2025年7月、NVIDIA が RISC-V RVA23 プロファイル上での CUDA ポーティングを発表
- **出荷実績**: SiFive の IP は500以上の設計に採用され、累計100億コア以上が出荷（2026年時点）
- **IPO 準備**: SiFive CEO が Series G を最後の民間ラウンドと明言し、IPO を示唆

### Arm との比較

| 観点 | RISC-V | [[Arm]] |
|------|--------|---------|
| エコシステム成熟度 | 成長中（ツール・ライブラリ拡充中） | 非常に成熟（数十年の蓄積） |
| 性能 | ハイエンドは開発中 | Apple Silicon・Graviton で実証済み |
| コスト | ライセンスフリー | ライセンス+ロイヤリティ |
| カスタマイズ性 | 極めて高い | 限定的 |
| 主な強み | IoT・組み込み、AI 特化チップ | モバイル・PC・サーバー |

## ポイント

- オープンソース ISA のためロイヤリティフリー。チップコストの削減と設計の自由度が最大の利点
- モジュラー設計によりベース ISA + 必要な拡張のみを選択可能
- NVIDIA の CUDA 移植と SiFive への投資により、データセンター市場への進出が加速
- 車載分野では Quintauris を通じた標準化が進行中
- エコシステムの成熟度では [[Arm]] に劣るが、急速に改善中

## 関連項目

- [[Arm]] - RISC-V の主要な競合。プロプライエタリ ISA としてモバイル・サーバー市場を支配
- [[AI専用チップ]] - RISC-V のカスタム拡張で AI 特化プロセッサを設計可能

## 参考

- [RISC-V - Wikipedia](https://en.wikipedia.org/wiki/RISC-V)
- [RISC-V International](https://riscv.org/)
- [SiFive to Power Next-Gen RISC-V AI Data Centers with NVIDIA NVLink Fusion](https://www.sifive.com/press/sifive-nvidia-nvlinkfusion-datacenter)
- [RISC-V vs ARM: Complete Architecture Comparison Guide 2026 | Stromasys](https://www.stromasys.com/resources/risc-v-vs-arm-processors-comparative-analysis/)
- [RISC-V vs ARM: A Comprehensive Comparison | Wevolver](https://www.wevolver.com/article/risc-v-vs-arm)
