---
title: "NVIDIA RTX Spark N1X"
date: 2026-11-09
tags:
  - AI
  - LLM
related:
  - "[[Arm]]"
  - "[[CPU]]"
  - "[[GPU]]"
  - "[[LLM実行環境]]"
  - "[[Ollama]]"
---

## 概要

NVIDIA RTX Spark は，NVIDIA と Microsoft が 2026-05-31 に発表した Windows PC 向けの Arm ベース「スーパーチップ」である．CPU 設計に MediaTek が協力し，Blackwell GPU と最大 128GB のユニファイドメモリを 1 パッケージに載せる．報道では「N1X」と呼ばれ，Linux 向けに先行して出荷された GB10（DGX Spark 搭載）と同一シリコンとされる（NVIDIA 公式の明言は未確認）．

## 詳細

### 名称の対応

| 名称 | 位置づけ | 一次情報での確認 |
|------|----------|------------------|
| RTX Spark | NVIDIA 公式プレスリリースのチップ名（Windows PC 向けプラットフォーム） | NVIDIA ニュースルームで確認 |
| N1X | RTX Spark の開発コードネーム由来の名称．ServeTheHome は「RTX Spark N1X」を公式チップ名と記載 | NVIDIA プレスリリースには N1X の語なし（二次情報） |
| GB10 | DGX Spark などで先行出荷されている同一 SoC | ServeTheHome が NVIDIA ブリーフィングとして記載（二次情報） |
| N1 | リーク段階で言及された下位モデル | 公式未確認 |

### 仕様

NVIDIA 公式（プレスリリース）で確認できたもの:

- 20 コアの「NVIDIA Grace CPU」，NVLink-C2C で GPU と接続，MediaTek が「custom CPU design」に協力
- Blackwell RTX GPU，CUDA コア 6,144，第 5 世代 Tensor Core（FP4 対応）
- 最大 128GB のユニファイドメモリ
- 最大 1 PFLOP の AI 性能
- ノート PC は 14〜16 インチ，薄さ最小 14mm，重さ約 3 ポンド

ServeTheHome（Ryan Smith 氏の分析記事）による詳細:

- TSMC 3nm，CPU/GPU の 2 チップレット，NVLink-C2C 600 GB/s
- CPU は Cortex-X925 x10 + Cortex-A725 x10 の計 20 コア．Arm の 2024 年世代の既製コアで，Grace ベースではないとしている（NVIDIA の「Grace」表記と食い違う）
- メモリは 256-bit，LPDDR5X（最大 128GB），帯域は約 300 GB/s（LPDDR5X-9400）
- GPU は 48 SM / 6,144 CUDA コアで RTX 5070 相当のハードウェア（ただしメモリ帯域は 5070 の半分以下）
- ディスプレイコントローラと PCIe 等の I/O は CPU ダイ側

### 発表と出荷状況

- 2026-05-31〜06-01: GTC Taipei で発表．Microsoft Build（6/2-3）で Windows のエージェント機能を紹介
- 提携 OEM: ASUS，Dell，HP，Lenovo，Microsoft Surface，MSI．Acer と GIGABYTE は後続
- Microsoft Surface Laptop Ultra（15 インチ）: 2026-10-07 に価格発表，10/16 出荷開始（Windows Latest 経由の二次情報）
  - 18 コア CPU / 5,120 CUDA コア / 24〜32GB 構成が $2,599.99 から
  - 20 コア / 6,144 CUDA コア / 32〜128GB 構成が $3,699.99〜$5,899.99
  - 下位構成が 18 コア・5,120 CUDA コアであることから，フル仕様より削減した派生品が存在する（NVIDIA 公式の仕様表では未確認）
- Microsoft Surface RTX Spark Dev Box（Windows ミニ PC）も存在（ServeTheHome）

### ソフトウェア面

- Microsoft は Windows のスケジューラと Prism エミュレータをこのチップ向けに調整したと発表
- 新しい Windows セキュリティプリミティブ（identity，containment，policy）と，エージェント実行用ランタイム NVIDIA OpenShell
- x86 アプリは Prism エミュレーション，ネイティブ Arm 版があればそちらを使う．Adobe が Photoshop / Premiere を最適化

### 噂・リーク情報（公式発表と区別）

- 発表前に N1X / N1 のスペックがリーク（OpenCL ベンチ等）し，「RTX 5070 並みの CUDA コア数」と報じられた．実際の仕様は上記のとおりほぼ一致
- Jensen Huang 氏が GB10 と N1/N1X は同一シリコンと述べたとする報道（Tom's Hardware，本文は未取得）

## ポイント

- 公式名称は RTX Spark．N1X は報道・開発コードネームとして広く使われるが，NVIDIA プレスリリースには出てこない
- 報道では GB10（DGX Spark）と同一シリコンとされ，DGX OS（Linux）向けと Windows 向けの 2 市場に展開する構成と見られる
- CPU の中身は要注意: NVIDIA は「Grace CPU」，ServeTheHome は「Cortex-X925/A725 の既製コア」と説明しており，一次情報だけでは確定できない
- GPU は RTX 5070 相当の演算資源だがメモリ帯域は約 300 GB/s で，最大 128GB のユニファイドメモリによるローカル LLM 推論が売り
- 独立ベンチマークと実機の評価はまだ限定的で，NVIDIA の性能値（1 PFLOP は FP4 のスパース性能と推測されるが公式未確認）は鵜呑みにしない

## 関連項目

- [[Arm]]
- [[CPU]]
- [[GPU]]
- [[LLM実行環境]]
- [[Ollama]]

## 参考

- [NVIDIA and Microsoft Reinvent Windows PCs for the Age of Personal AI（NVIDIA ニュースルーム）](https://nvidianews.nvidia.com/news/nvidia-microsoft-windows-pcs-agents-rtx-spark)
- [NVIDIA RTX Spark N1X launch overview（ServeTheHome）](https://www.servethehome.com/nvidia-rtx-spark-n1x-launch-overview-nvidia-arms-windows/)
- [Surface Laptop Ultra 価格・構成（Windows Latest）](https://www.windowslatest.com/2026/10/07/microsofts-macbook-pro-killer-has-up-to-128gb-ram-starts-at-2599-and-its-called-surface-laptop-ultra/)
