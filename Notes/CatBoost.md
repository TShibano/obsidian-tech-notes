---
title: "CatBoost"
date: 2026-02-13
tags:
  - AI
  - ML
related:
  - "[[MLOps]]"
  - "[[Large Tabular Model]]"
---

## 概要

CatBoostはYandexが開発・オープンソース化した勾配ブースティングライブラリ。"Category Boosting"の略で、カテゴリカル変数を前処理なしでネイティブに扱える点が最大の特徴。XGBoost・LightGBMと並ぶ代表的な決定木ブースティングアルゴリズムで、表形式データの機械学習タスクにおいてSOTAレベルの性能を発揮する。

## 詳細

### 主な特徴

#### 1. カテゴリカル変数のネイティブサポート

XGBoostやLightGBMはカテゴリカル変数をOne-Hotエンコーディング等で数値変換する必要があるが、CatBoostは**Ordered Target Statistics**という手法で自動的に数値化する。データリークを防ぐためにランダム置換（permutation）を使いながらカテゴリ→統計量のマッピングを学習する。

#### 2. Ordered Boosting（順序付きブースティング）

通常の勾配ブースティングでは、勾配計算に使ったデータで同じ木を学習するため予測シフト（過学習の一因）が発生する。CatBoostはデータをランダムに置換し、各サンプルの勾配計算に「それ以前のサンプルのみで学習した木」を使うことで予測シフトを抑制する。

#### 3. 対称木（Symmetric Tree）

CatBoostが採用する**対称決定木**は、同一深さのすべてのノードが同じ分割条件を使う。この構造により：
- CPU/GPUでの推論が高速
- 過学習への正則化効果
- 浅い木でも高い表現力

### XGBoost・LightGBMとの比較

| 項目 | CatBoost | XGBoost | LightGBM |
|------|----------|---------|---------|
| カテゴリカル変数 | ネイティブ対応 | 要前処理 | 部分対応 |
| 学習速度 | 中程度 | 遅い | 最速 |
| 推論速度 | 高速（対称木） | 普通 | 普通 |
| ハイパラ調整 | 少ない（ほぼデフォルトで動く） | 多い | 多い |
| 大規模データ | 可 | 可 | 最適 |
| GPU対応 | ○ | ○ | ○ |

**使い分けの目安:**
- カテゴリカル変数が多い → **CatBoost**
- 大規模データ・高速学習が優先 → **LightGBM**
- 汎用・Kaggle実績重視 → **XGBoost**

### Python での使用例

```python
# インストール
# pip install catboost

from catboost import CatBoostClassifier, Pool

# 学習データ
X_train = [[1, "a", 2.5], [2, "b", 3.1], [3, "a", 1.8]]
y_train = [0, 1, 0]
cat_features = [1]  # カテゴリカル変数の列インデックス

# モデル作成・学習
model = CatBoostClassifier(
    iterations=500,
    learning_rate=0.1,
    depth=6,
    cat_features=cat_features,
    verbose=100
)

train_pool = Pool(X_train, y_train, cat_features=cat_features)
model.fit(train_pool)

# 予測
predictions = model.predict([[4, "b", 2.0]])
probabilities = model.predict_proba([[4, "b", 2.0]])
```

### 主なハイパーパラメータ

| パラメータ | デフォルト | 説明 |
|-----------|----------|------|
| `iterations` | 1000 | ブースティングのラウンド数 |
| `learning_rate` | 自動 | 学習率（小さいほど精度向上・過学習抑制） |
| `depth` | 6 | 木の深さ（対称木なので6〜10が実用的） |
| `l2_leaf_reg` | 3.0 | L2正則化係数 |
| `cat_features` | - | カテゴリカル変数の列インデックスまたは名前 |

### ユースケース

- Kaggleコンペ（特にカテゴリカル変数が多いテーブルデータ）
- Eコマースのクリック予測・推薦システム（Yandex本体での使用実績）
- 与信スコアリング・金融予測
- 気象予測・工業データのアノマリー検知

## ポイント

- カテゴリカル変数の前処理が不要なため、特徴量エンジニアリングのコストを削減できる
- デフォルト設定でも高い精度が出るため、ベースラインモデルの構築に適している
- LightGBMと比べて学習は遅いが、予測推論は対称木のおかげで高速
- scikit-learn互換APIを持つため、既存のMLパイプラインに容易に組み込める
- GPUによる学習加速（`task_type="GPU"`）でXGBoostより大幅に高速化できる

## 関連項目

- [[MLOps]]
- [[Large Tabular Model]]

## 参考

- [CatBoost 公式サイト](https://catboost.ai/)
- [GitHub - catboost/catboost](https://github.com/catboost/catboost)
- [When to Choose CatBoost Over XGBoost or LightGBM - neptune.ai](https://neptune.ai/blog/when-to-choose-catboost-over-xgboost-or-lightgbm)
- [CatBoost Documentation](https://catboost.ai/docs/)
