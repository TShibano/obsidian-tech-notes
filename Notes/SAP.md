---
title: "SAP"
date: 2026-11-09
tags:
  - Cloud
  - AI
  - Agent
  - DB
related:
  - "[[S/4HANA]]"
  - "[[SAP HANA]]"
  - "[[SAP BTP]]"
  - "[[ABAP]]"
  - "[[Joule]]"
  - "[[Large Tabular Model]]"
  - "[[Decision Model]]"
  - "[[MCP]]"
  - "[[Databricks]]"
  - "[[Snowflake]]"
  - "[[データレイクハウス]]"
  - "[[マスターデータ管理]]"
  - "[[OLTP]]"
  - "[[OLAP]]"
  - "[[SAML]]"
  - "[[OpenID Connect]]"
  - "[[LDAP]]"
  - "[[Active Directory]]"
  - "[[エンタープライズアーキテクチャ]]"
---

## 概要

SAP SE は，ドイツ・ヴァルドルフに本社を置く世界最大級の企業向けソフトウェアベンダーで，ERP（基幹業務システム）の事実上の標準を築いた企業である．現在の主力は SAP S/4HANA（インメモリ DB の SAP HANA 上で動く ERP）で，旧世代の SAP ECC の保守終了（2027 年問題）を背景に，クラウド移行（RISE with SAP）と AI（Joule）を軸に事業を再編している．

## 詳細

### 歴史と製品世代

- 1972 年，旧 IBM の技術者 5 人が設立．社名は Systemanalyse Programmentwicklung（システム分析・プログラム開発）の頭字語．リアルタイム処理の標準業務ソフトを目指した
- 1970 年代末〜1980 年代に R/2（メインフレーム），その後 R/3（クライアント/サーバ，3 階層）で ERP の世界標準になった
- R/3 の後継が SAP ERP Central Component（ECC）．ECC 5.0 が 2004 年
- 2011 年に SAP HANA，2015 年に SAP S/4HANA を発表（ECC の後継）

| 世代 | 概要 | 基盤 DB |
|------|------|---------|
| R/2 | メインフレーム向け | 独自 |
| R/3 | 3 階層クライアント/サーバ | 各種 RDB |
| ECC（ERP 6.0 / Business Suite 7） | R/3 の後継．拡張パッケージ（EhP）で機能追加 | 各種 RDB（Oracle，DB2 等） |
| S/4HANA | ECC の再設計．データモデル簡素化（例: 会計データの Universal Journal） | SAP HANA 専用 |

### 構成要素と用語（技術者向け）

**主要モジュール（業務領域）**

| 略称 | 名称 | 内容 |
|------|------|------|
| FI | Financial Accounting | 財務会計（総勘定元帳，売掛・買掛，固定資産） |
| CO | Controlling | 管理会計（原価センタ，収益性分析） |
| SD | Sales and Distribution | 販売管理（受注，出荷，請求） |
| MM | Materials Management | 資材管理・購買・在庫 |
| PP | Production Planning | 生産計画・製造 |
| QM / PM | Quality / Plant Maintenance | 品質管理 / 設備保全 |
| HCM | Human Capital Management | 人事（クラウドは SuccessFactors が主軸） |
| WM / EWM | Warehouse Management | 倉庫管理 |

- S/4HANA では SD の一部が Advanced ATP 等に再編されるなど，モジュール構成が整理されている（詳細は製品別の Simplification List で確認する）
- 各モジュールの細かな統合範囲は既存知識に基づく整理であり，実装時は公式ドキュメントで確認する

**技術基盤・用語**

- **ABAP**: SAP 独自のプログラミング言語（Advanced Business Application Programming）．業務ロジックとカスタム開発の中心．クラウド向けは「ABAP Cloud」（公開された API のみ利用可）
- **RAP（RESTful ABAP Programming Model）**: S/4HANA で Fiori アプリ・サービス・API を作る標準のプログラミングモデル（SAP の ABAP Cloud FAQ より）
- **CDS（Core Data Services）**: データモデル定義．ABAP Cloud では SAP テーブルへ直接アクセスせず CDS ビュー経由が前提
- **SAP Fiori / SAPUI5**: SAP の UX 標準．従来の SAP GUI（トランザクションコード）に代わる Web UI
- **SAP HANA**: インメモリ列指向 DB．OLTP と OLAP の両方を 1 つの DB で扱う
- **SAP BTP（Business Technology Platform）**: 拡張・統合・データ・AI のためのクラウド基盤．CAP（Cloud Application Programming Model）で Node.js/Java の side-by-side 拡張を作る
- **Clean Core**: 標準コアを改変せず，拡張は公開 API 経由で ABAP Cloud（in-app）や BTP（side-by-side）に出すことでアップグレード容易性を保つ方針
- **SAP Business Data Cloud（BDC）**: SAP のデータ基盤．Databricks との連携（SAP Databricks）を含む

### 提供形態

| 形態 | 概要 |
|------|------|
| SAP Cloud ERP Public Edition | マルチテナント SaaS．標準準拠（fit-to-standard） |
| SAP Cloud ERP Private Edition | シングルテナント．旧称 S/4HANA Cloud Private Edition，RISE with SAP の中核 |
| S/4HANA On-Premise | 自社運用．リリースは年次の 4 桁バージョン（例: 2025） |

RISE with SAP は 2021 年 1 月開始のサブスクリプション型パッケージ（ERP + インフラ運用 + BTP 等）．なお 2025 年に RISE の呼称が SAP Cloud ERP Private Edition に置き換えられたとの報道がある（The Register）．

### ECC 2027 年問題

- ECC 6.0（EhP6〜8）のメインストリーム保守は **2027 年末**に終了する．SAP は期限は変更しないと明言している
- 延長保守（Extended Maintenance）が **2030 年末**まで有料で提供される．上乗せは 2% とする報道が多いが，契約により異なる可能性がある
- 大規模・複雑な環境向けに，**2031〜2033 年末**の「SAP ERP, Private Edition, Transition Option」が発表されている．クラウドサブスクリプション契約が条件で，一般的な保守延長ではない
- S/4HANA 自体は SAP が 2040 年までの提供を約束している（2024 年 10 月発表）
- 移行状況: 2024 年末時点で ECC 顧客約 35,000 社のうち 39% が S/4HANA 移行のライセンスを契約済み（前年 34%）．SAP のオンプレ保守売上は 2025 年に 105 億ユーロで，2022 年の目標 85 億ユーロを約 20 億ユーロ上回った（The Register）
- DSAG の調査では，ドイツ語圏の ECC ユーザーの約半数が 2027 年以降も ECC への投資を続ける予定．Gartner は 2030 年時点でも 1 万社超が ECC 上で主要業務を動かすと予測
- 第三者保守（Rimini Street 等）という選択肢もあるが，SAP 公式のサポートではない

移行アプローチの典型は 3 種類:

1. **ブラウンフィールド**（システムコンバージョン）: 既存を引き継いで S/4HANA へ
2. **グリーンフィールド**: 新規導入．業務を標準に寄せる
3. **ブルーフィールド**（選択的移行）: データ・プロセスを選んで移行

### AI の最新動向（2026-11 時点）

- **Joule**: SAP の AI アシスタント．**Joule Work**（ゴールを指示して AI エージェントに実行させる入口）と **Joule Studio**（エージェントの構築）が段階的に一般提供中（erp.today の TechEd 2026 報道）．Joule Studio のランタイムは一部顧客に 2026 年 12 月まで無償
- **SAP Business AI Platform** と **Business Data Cloud**: BDC のレイクハウス部分は 2027 年第 1 四半期に GA 予定，Ontology Manager はベータ予定（同報道）．プレミアムの Enterprise AI Gateway は 2027 年第 1 四半期予定
- **SAP-RPT**（表形式データ向け基盤モデル）: RPT-1 は generative AI hub で利用可能．RPT-TS（時系列）は GA，RPT-2.0（異常検知対応）は 2027 年第 1 四半期予定（同報道）．[[Large Tabular Model]] の一例
- **開発者向け AI**: ABAP 向けの MCP サーバや ABAP 特化 LLM の計画が報じられているが，提供時期の情報源間で食い違いがあり未確認
- **業績（2026 年 Q3，10 月 21 日発表）**: 現行クラウドバックログ 241 億ユーロ（前年同期比 +28%，為替一定で +26%），クラウド売上 +26%（為替一定 +25%），総売上は初めて四半期 100 億ユーロ超

## ポイント

- SAP は ERP の事実上の標準．R/3 → ECC → S/4HANA と進化し，S/4HANA は SAP HANA 専用
- 2027 年問題: ECC のメインストリーム保守は 2027 年末，延長保守は 2030 年末まで．大規模顧客向けに 2033 年までの Transition Option がある
- 技術者視点のキーワードは ABAP Cloud / RAP / CDS / Fiori / BTP / CAP / Clean Core．「コアを汚さない」拡張が移行後の設計原則
- AI は Joule（アシスタント → エージェント）と Business Data Cloud（データ基盤）が柱．一部は 2027 年にかけての計画段階であり，GA 状況は公式で都度確認する
- 日本でも ECC 保有企業が多く，移行はコスト・要員（ABAP 技術者不足）面で大きな課題

## 関連項目

- [[S/4HANA]]
- [[SAP HANA]]
- [[SAP BTP]]
- [[ABAP]]
- [[Joule]]
- [[Large Tabular Model]]
- [[Decision Model]]
- [[MCP]]
- [[Databricks]]
- [[Snowflake]]
- [[データレイクハウス]]
- [[マスターデータ管理]]
- [[OLTP]]
- [[OLAP]]
- [[SAML]]
- [[OpenID Connect]]
- [[LDAP]]
- [[Active Directory]]
- [[エンタープライズアーキテクチャ]]

## 参考

- [SAP の歴史（SAP 公式）](https://www.sap.com/about/company/history.html)
- [Embracing the Future: A Strategic Shift to Cloud ERP Solutions（SAP News, 2024-10）](https://news.sap.com/2024/10/embracing-strategic-shift-cloud-erp/)
- [SAP Q3 2026 結果（SAP News）](https://news.sap.com/?p=248420)
- [SAP extends support deadline（The Register, 2025-01-28）](https://www.theregister.com/2025/01/28/sap_extends_support_deadline/)
- [SAP's grand cloud escape plan €2B short of the runway（The Register, 2026-03-19）](https://www.theregister.com/2026/03/19/sap_2b_off_target/)
- [SAP TechEd 2026: What's Rolling Out and What's Planned Through 2027（erp.today）](https://erp.today/sap-teched-2026-whats-rolling-out-and-whats-planned-through-2027/)
- [ABAP Cloud FAQ（SAP Community）](https://pages.community.sap.com/topics/abap/abap-cloud-faq)
- [Navigating Your RISE with SAP Journey: Transition Option（SAP News）](https://news.sap.com/?p=233946)
