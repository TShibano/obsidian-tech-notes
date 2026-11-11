---
title: "AIStor"
date: 2026-11-09
updated: 2026-11-11
tags:
  - Cloud
  - AI
  - Tool
related:
  - "[[MinIO]]"
  - "[[オブジェクトストレージ]]"
  - "[[データレイク]]"
  - "[[データ基盤]]"
  - "[[RustFS]]"
  - "[[Garage]]"
  - "[[SeaweedFS]]"
  - "[[Ceph]]"
---

## 概要

AIStor（MinIO AIStor）は，MinIO 社が提供する商用の S3 互換[[オブジェクトストレージ]]製品である．2024年11月に旧「MinIO Enterprise Object Store」から発展する形で発表され，オブジェクトに加えて Iceberg テーブルやエージェント向けメモリを扱う「AI 向けデータ基盤」として位置づけられている．OSS 版の [[MinIO]] が2025年末から2026年にかけてメンテナンス終了・アーカイブとなったため，MinIO 社の開発は事実上 AIStor に一本化されている．

## 詳細

### OSS 版 MinIO との関係と経緯

OSS 版（Community Edition，AGPLv3）の基本機能やライセンスは [[MinIO]] を参照．ここでは AIStor 移行に至る経緯のみ整理する．

| 時期         | 出来事                                                                                                                            |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------ |
| 2025年2月26日 | 共同創業者が GitHub にコミットし，管理 UI（アカウント・ポリシー・設定・ライフサイクル・サイトレプリケーション・バケット管理）を削除．オブジェクトブラウザのみ残し，管理は `mc` に限定．フル機能の Web コンソールは AIStor のみ |
| 2025年10月中旬 | Docker イメージの公開を停止（Linuxiac による報道）                                                                                              |
| 2025年12月初旬 | OSS プロジェクトを「メンテナンスモード」へ移行．新機能・PR・コントリビューションの受付を停止し，重大なセキュリティ修正のみ個別判断で対応                                                        |
| 2026年2月ごろ  | README が「THIS REPOSITORY IS NO LONGER MAINTAINED.」に変更（日付は二次情報では 2026-02-12 とされる）                                               |
| 2026年4月25日 | GitHub リポジトリがアーカイブ（read-only）に                                                                                                 |

- 現在の README は「Community Edition は source code only で配布」と明記し，ビルド済みバイナリは更新されない（レガシー扱い）．導入は `go install` か Dockerfile からのビルド
- README は代替として **AIStor Free**（単一ノードのフル機能版，フリーライセンス）と **AIStor Enterprise**（分散版，商用サポート付き）を案内している
- MinIO 社は AIStor を「オープンソースに縛られない有償プラットフォーム」と位置づけており，OSS から AIStor への誘導が意図されている

### AI/ML ワークロード向け機能

| 機能                | 内容                                                                                                                                                                                                      |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **promptObject**  | S3 API の拡張．非構造化オブジェクトに対し自然言語プロンプトで問い合わせる（「PUT and GET」から「PUT and PROMPT」へ）．2024年11月の発表時の目玉機能                                                                                                            |
| **AIHub**         | Hugging Face API 互換の，AIStor 内のプライベートなモデル・データセットリポジトリ．コード変更なしで利用可能とされる                                                                                                                                   |
| **S3 over RDMA**  | GPU/ホストのメモリとサーバー間でカーネルのネットワークスタックを介さずデータ転送（GPUDirect 的）．RDMA 経路があるのは `GetObject` / `PutObject` / `UploadPart` のみ．InfiniBand または RoCEv2 が必要で，RDMA ビルド（`minio.rdma`）を使う．リクエスト単位でネゴシエートされ，HTTP クライアントと併用可能 |
| **ノード間 RDMA**     | イレージャコーディングのシャードをノード間で RDMA 転送する別系統の機能．別途有効化し，ロスレスな RoCEv2 ファブリックが必要                                                                                                                                    |
| **AIStor Tables** | オブジェクトストア自体が Apache Iceberg REST catalog を提供し，別カタログサービスが不要．2026年2月3日に GA（報道ベース．一次情報は未確認）．後に分散コンパクション，スナップショット期限切れ，不要ファイル削除，タグベースのアクセス制御，サイトレプリケーションを含む形で stable 系列にも導入                                  |
| **AIStor Memory** | 2026年7月29日発表．オブジェクト・テーブルに並ぶ第3のネイティブデータ型で，AI エージェントのメモリ・ワークスペース状態・シークレットを保持．HTTPS または POSIX マウントでアクセス                                                                                                    |
| **その他**           | バケット単位 QoS，クォータ，バージョニング，オブジェクトロック（WORM），バケットインベントリ（CSV/JSON/Parquet），ライフサイクル，SFTP/FTPS/FTP 対応，Global Console，Kubernetes Operator，MCP サーバー（2024年の launch 時の報道）                                           |

#### S3 over RDMA のベンチマーク（ベンダー報告値）

- 生の配信: VRAM へ 42.4 GB/s（CPU 0.95 コア）．チューニング済み HTTP ローダーは 39.4 GB/s（68.2 コア）
- 16 GiB のチェックポイント: RDMA 0.65 秒，チューニング済み HTTP 12.16 秒，素の `torch.save` 62.55 秒
- Llama-3.1-8B の重み 16 GB のロード: RDMA 0.77 秒，ダウンロード後ロード 30.96 秒
- 環境は ConnectX-7 400GbE（RoCEv2），8× H200．クライアントは CUDA Toolkit 13.1.1 以降の cuObject
- 制約: GPU がそのまま使えるペイロードに限る（エンコード済み JPEG は恩恵なし）．ピン留めされていないホストメモリは TCP へ暗黙にフォールバックする

### 価格・提供形態

2025年12月23日に3段階のサブスクリプションが発表された．

| 項目   | Free                | Enterprise Lite   | Enterprise                        |
| ---- | ------------------- | ----------------- | --------------------------------- |
| 構成   | 単一ノード（ドライブ数はハード次第）  | マルチノード，水平スケール     | マルチノード，水平スケール                     |
| 容量   | ハード次第               | 400 TiB 未満        | 上限記載なし（Lite 超過時は自動で Enterprise へ） |
| サポート | コミュニティ Slack・ドキュメント | セルフサポート（有償アドオンあり） | 24/7/365 の直接エンジニアサポート             |
| SLA  | なし                  | 5日未満（アドオンのみ）      | 4時間未満（Panic Button）               |
| 価格   | 無料．再配布・再販は制限        | サブスクリプション（非公開）    | サブスクリプション（非公開）                    |

- 課金は生容量ではなく**使用可能容量**ベースで，容量に応じたボリュームディスカウントがある．具体額は営業問い合わせ
- 提供形態はオンプレミス・クラウド・エッジ．AWS Marketplace にも出品されている
- 既存ノート [[MinIO]] にある「年間 $96,000〜（400 TB まで）」は今回の一次情報では確認できていない

### ライセンス

AIStor は**商用ライセンス**の製品で，OSS 版 [[MinIO]] の AGPLv3 は適用されない．料金ページは「MinIO AIStor is commercially licensed」とし，AGPLv3 版は「no longer maintained. Its codebase is frozen」として現行の選択肢から外している．Free も含め，ソースコードは公開されていない（プロプライエタリ）．

#### 適用される契約

| 対象 | 契約 | 最終更新 |
| --- | --- | --- |
| AIStor Free | MinIO AIStor Free Tier License Agreement | 2026-01-30 |
| Enterprise Lite / Enterprise | MinIO Customer License and Subscription Agreement（SUBNET サブスクリプション） | 未確認 |
| AWS Marketplace 等のイメージ | Marketplace EULA（BYOL．ライセンスは MinIO から直接取得） | 2025-01-16 |
| OEM・MSP（組み込み・再販） | OEM Agreement / OEM MSP Agreement | 未確認 |

#### AIStor Free の契約内容

- **許諾範囲**: 限定的，非独占，譲渡不可，ロイヤリティなし．使えるのは **standalone mode のみ**（分散クラスタリングも高可用性もない単一ノード構成）
- **商用利用**: 分散モードが不要な構成なら「for any purpose, including commercial production workloads」で，本番の商用利用も可
- **容量**: 契約上の上限はない（発表ブログも「no artificial capacity limit」）
- **禁止事項**
  - 改変，リバースエンジニアリング，逆コンパイル，派生物の作成
  - 第三者への配布，サブライセンス，貸与，再販，再配布（料金ページにも「Redistribution is prohibited」）
  - 著作権表示の削除
  - SaaS としてのホスティングとベンチマーク結果の公開は，契約に記載がない
- **保証・責任**: 「AS IS」で無保証．SLA もサポート義務もない．責任上限は「直近12か月に支払った額」で，Free では実質ゼロ
- **期間・解除**: ダウンロードした時点で発効する．違反があれば MinIO は即時に解除でき，解除後は利用を止めて全コピーを破棄する義務がある
- **契約の変更**: MinIO は契約を随時改定でき，改定後も使い続けると同意したものとみなされる
- **準拠法**: カリフォルニア州法．専属管轄はサンタクララ郡の州裁判所または連邦裁判所
- **機能の制限**: ドキュメントでは，分散デプロイ，レプリケーション，ティアリング，保存時暗号化，テレメトリ，`mc support` などは Enterprise Lite / Enterprise 限定．Free で保存時暗号化を使おうとすると `XMinioPaidTierLicenseRequired`（HTTP 405）で拒否される

> [!warning] 公式情報の食い違い
> 料金ページの FAQ は Free を「full AIStor feature set」とし，発表ブログは「no limits」「unrestricted single-node option」と書いている．一方，契約は standalone に限定し，ドキュメントは上記の機能を有償版限定としている．判断の根拠には契約とドキュメントを使う．

#### Marketplace EULA（BYOL イメージ）

- 許諾は「internal business operations」（社内業務）のための利用に限られる．複製，改変，配布，リバースエンジニアリングは禁止
- MinIO は利用状況を監査する権利を持つ
- MinIO から有効なライセンスを取得するとこの EULA は自動で終了し，以後は Customer License and Subscription Agreement が適用される

#### ライセンスキーの運用

- AIStor は「active and unexpired」なライセンスを必要とする．キーは SUBNET（Deployments > License Key）からキー文字列か `minio.license` ファイルで取得し，`mc license update ALIAS [ファイル]` や Helm の `--set license=...` で適用する．Linux では各ノードの `/opt/minio/minio.license` を置き換えると，24時間以内（再起動すれば即時）に反映される
- `mc license info ALIAS` は Organization，Plan（試用版は「(Trial)」付き），Org-level Capacity Limit，Issued，Expiry，License ID などを表示する．Free のライセンスは**期限なし**（Expiry は N/A）
- ライセンスには利用できる機能（Objects，Tables，Memory，Files）と組織単位の容量上限が書かれている．容量上限を超えると猶予なしで書き込みが止まる（N/A なら容量の強制はない）
- 有償ライセンスが切れたときの段階的な制限（サーバプロセス自体は停止しない）:

| 期限切れ後 | 状態 |
| --- | --- |
| 期限前30日 | 警告を表示 |
| 0〜30日 | 猶予期間．全機能を使える |
| 30〜90日 | 読み取り専用．書き込みは HTTP 403 `AccessDenied` |
| 90日以降 | オフライン．S3 操作はすべて止まり，Admin API，Console，ヘルスチェック，メトリクスだけが動く |

- 試用版ライセンス（Enterprise Lite / Enterprise とも60日）には猶予期間がない．期限切れで読み取り専用になり，60日でオフラインになる
- RELEASE.2025-12-20T04-58-37Z 以降は，ライセンスがなくてもオフラインモードで起動する（それより前は起動しない）．期限切れのライセンスは SUBNET から24時間ごとに自動更新を試みる
- エアギャップ環境での運用はドキュメントに明記がない（ファイル適用はできるが，更新は SUBNET 経由が前提）

#### OSS 版（AGPLv3）との比較

| 観点 | MinIO Community Edition | AIStor Free | AIStor Enterprise Lite / Enterprise |
| --- | --- | --- | --- |
| ライセンス | AGPLv3（OSS） | 独自の無償ライセンス（プロプライエタリ） | 商用サブスクリプション |
| ソースコード | 公開（アーカイブ済み） | 非公開 | 非公開 |
| 改変・派生物 | 可（ネットワーク越しに提供するならソース開示義務あり） | 禁止 | 禁止 |
| 再配布 | 可（AGPLv3 の条件で） | 禁止 | 禁止（OEM 契約は別途） |
| 構成 | 分散も可 | 単一ノードのみ | 分散も可（Lite は 400 TiB 未満） |
| ライセンスキー | 不要 | 必要（期限なし） | 必要（期限あり） |
| 保守 | 終了 | MinIO が継続 | MinIO が継続＋サポート |

## ポイント

- AIStor は MinIO 社の商用製品で，OSS 版の開発は2025年末に事実上終了，2026年4月にリポジトリがアーカイブされた
- OSS 版は source-only 配布で，バイナリ・Docker イメージの公式提供はない．本番利用は自前ビルドかフォーク，他実装への移行が必要
- 2025年12月以降は単一ノード無料の AIStor Free が公式の「コミュニティ向け」選択肢．商用の本番利用もできるが，standalone 限定で，改変・再配布・再販を禁じるプロプライエタリなライセンスであり，AGPLv3 ではない
- 有償ライセンスは期限が切れると，30日後に読み取り専用，90日後に S3 操作が全停止する．更新管理が運用上の要点になる
- AI 向けの差別化点は S3 over RDMA，promptObject，AIHub，Iceberg（Tables），Memory．数値は多くがベンダー自己計測
- 無償で OSS を維持したい場合は [[RustFS]]，[[Garage]]，[[SeaweedFS]]，[[Ceph]] などが代替候補

## 関連項目

- [[MinIO]] — OSS 版（Community Edition）と経緯
- [[オブジェクトストレージ]]
- [[データレイク]]
- [[データ基盤]]
- [[RustFS]] — MinIO 代替
- [[Garage]]
- [[SeaweedFS]]
- [[Ceph]]
- [[Apache Iceberg]]
- [[RDMA]]

## 参考

- [minio/minio（README・アーカイブ状況）](https://github.com/minio/minio)
- [Introducing New Subscription Tiers for MinIO AIStor（2025-12-23）](https://www.min.io/blog/introducing-new-subscription-tiers-for-minio-aistor-free-enterprise-lite-and-enterprise)
- [AI Workloads - AIStor docs](https://docs.min.io/aistor/operations/core-concepts/ai-workloads/)
- [Introducing S3 over RDMA in AIStor](https://www.min.io/blog/introducing-s3-over-rdma-in-aistor-object-storage-at-the-speed-of-memory)
- [Blocks & Files - MinIO releases AIStor with GPUDirect-like S3 over RDMA](https://www.blocksandfiles.com/ai-ml/2024/11/13/minio-releases-aistor-with-gpudirect-like-s3-over-rdma/1607868)
- [Blocks & Files - MinIO removes management features from community edition](https://blocksandfiles.com/2025/06/19/minio-removes-management-features-from-basic-community-edition-object-storage-code/)
- [Linuxiac - MinIO Ends Active Development, Steers Users Toward Paid AIStor](https://linuxiac.com/minio-ends-active-development/)
- [Futurum - MinIO Launches AIStor Memory](https://futurumgroup.com/insights/minio-launches-aistor-memory-for-agentic-ais-durable-context-layer/)
- [MinIO AIStor Free Tier License Agreement（最終更新 2026-01-30）](https://www.min.io/legal/aistor-free-agreement)
- [Licenses - AIStor docs](https://docs.min.io/aistor/operations/licenses/)
- [mc license info - AIStor docs](https://docs.min.io/aistor/reference/cli/mc-license/mc-license-info/)
- [MinIO Pricing](https://min.io/pricing)
- [Marketplace End User License Agreement（最終更新 2025-01-16）](https://min.io/marketplace-eula)
- [MinIO AIStor リリースノート RELEASE.2026-07-24](https://dl-0.minio.io/aistor/minio/release/notes/release-notes-RELEASE.2026-07-24T16-43-31Z.md?raw=true)

## 更新履歴

- 2026-11-11: 「ライセンス」節を追加（Free の契約内容，Marketplace EULA，ライセンスキーの運用と期限切れ時の挙動，OSS 版との比較）
