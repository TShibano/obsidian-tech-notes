---
title: "BASE特性"
date: 2026-05-05
tags:
  - DB
  - NoSQL
related:
  - "[[ACID特性]]"
  - "[[NoSQL]]"
  - "[[ポリグロットデータストア]]"
  - "[[ワイドカラム型]]"
  - "[[KVストア]]"
---

## 概要

BASE特性（BASE Properties）は，分散 NoSQL データベースが採用する設計思想で「Basically Available（基本的に利用可能）・Soft State（柔軟な状態）・Eventually Consistent（結果整合性）」の3要素からなる．ACID特性の厳格な一貫性よりも可用性とスケーラビリティを優先するトレードオフを表す概念である．

## 詳細

### 3つの構成要素

| 要素 | 説明 |
|------|------|
| **Basically Available（基本的に利用可能）** | 障害やネットワーク分断が発生しても，一部のノードが利用不能になるだけでシステム全体は応答し続ける |
| **Soft State（柔軟な状態）** | 常に一貫した状態を保つ必要はなく，同期が完了するまで一時的な不整合を許容する |
| **Eventually Consistent（結果整合性）** | 更新後に一定時間が経過すれば，最終的にすべてのレプリカが同じ状態に収束することを保証する |

### CAP 定理との関係

CAP 定理（Eric Brewer, 2000年）は，分散システムが「一貫性（Consistency）・可用性（Availability）・分断耐性（Partition Tolerance）」の3つを同時に完全に満たすことはできないと主張する：

```
         一貫性（C）
         /         \
       /             \
    CP                CA
（ACID 重視）      （現実には困難）
    /                   \
  /                       \
分断耐性（P）---------- 可用性（A）
                AP
           （BASE 重視）
```

分散システムは分断耐性（P）を避けられないため，一貫性（ACID）か可用性（BASE）の選択を迫られる．

### ACID vs BASE の比較

| 観点 | ACID | BASE |
|------|------|------|
| 一貫性 | 強い一貫性（即時） | 結果整合性（最終的に収束） |
| 可用性 | 一貫性のために犠牲になることがある | 高可用性を優先 |
| トランザクション | 厳格（全か無か） | 緩やか（部分的成功を許容） |
| スケール | 垂直スケール中心 | 水平スケール（分散ノード） |
| 整合性保証タイミング | 即時 | 非同期（遅延あり） |
| 代表的 DB | PostgreSQL，Oracle，MySQL | Cassandra，DynamoDB，MongoDB |

### 結果整合性の実装パターン

NoSQL データベースが結果整合性を実現するための代表的なメカニズム：

| メカニズム | 説明 |
|-----------|------|
| **非同期レプリケーション** | 書き込みをリーダーが受け付け，フォロワーに非同期で伝播 |
| **ベクタークロック** | 各ノードのバージョンを追跡し，競合する変更を検出 |
| **Quorum 読み書き** | 複数ノードから過半数の応答を得ることで整合性を向上（W + R > N） |
| **Last-Write-Wins（LWW）** | タイムスタンプが新しい方が勝つ競合解決戦略 |

### ユースケース

BASE特性が受け入れられる場面：
- **SNS のタイムライン**: 数秒の遅延で全フォロワーに反映されれば十分
- **IoT センサーデータ**: 大量書き込みで一時的な不整合は許容できる
- **カート・セッション**: 一時的なデータで即時整合性は不要
- **商品カタログ**: 秒単位の鮮度の遅れは業務上問題ない

ACID が必要な場面（BASE では不適）：
- 銀行送金・決済処理（資金の消失・二重引き落としは許容不可）
- 在庫管理（過剰販売の防止）
- 医療記録（正確性が生命に関わる）

## ポイント

- BASE は ACID の対比概念として提唱された（化学の BASE は酸（ACID）の反対）
- 「可用性を得るために一貫性を一時的に妥協する」というトレードオフを明示した概念
- Cassandra，DynamoDB，MongoDB などの NoSQL は BASE を採用するが，近年は ACID トランザクションも部分的に提供するようになっている
- 「結果整合性」はシステムが正常動作している限り最終的には整合するという保証であり，「不整合のまま放置する」ではない
- [[NewSQL]] は ACID + 水平スケールを両立する第3の選択肢として注目される

## 関連項目

- [[ACID特性]] — BASE の対比概念となる厳格なトランザクション特性
- [[NoSQL]] — BASE特性を採用するデータベースカテゴリ
- [[ポリグロットデータストア]] — BASE/ACID を使い分ける多様なデータストアの組み合わせ
- [[ワイドカラム型]] — BASE特性を採用する代表的な NoSQL（Cassandra 等）
- [[KVストア]] — BASE特性を採用する代表的な NoSQL（DynamoDB，Redis 等）

## 参考

- [ACID vs BASE Databases - Difference Between Databases | AWS](https://aws.amazon.com/compare/the-difference-between-acid-and-base-database/)
- [結果整合性 - Eventual Consistency と BASE 特性 | 未来学](https://www.mirai-gaku.com/repository/theory/eventual-consistency/)
- [【まとめ】ACID特性・BASE基準・CAP定理 | Qiita](https://qiita.com/gdet_hry/items/7abab3b48e248d5bd63c)
