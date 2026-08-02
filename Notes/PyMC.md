---
title: "PyMC"
date: 2026-08-02
tags:
  - AI
  - ML
  - Python
related:
  - "[[ベイズ統計]]"
  - "[[MCMC]]"
  - "[[変分推論]]"
  - "[[Stan]]"
  - "[[Python]]"
---

## 概要

PyMC は，Python 向けの確率的プログラミングライブラリ．事前分布と尤度を直感的な API で記述するだけで，内部で高性能な NUTS サンプラーによる[[MCMC]]サンプリングを自動実行し，[[ベイズ統計]]モデルの事後分布を得られる．2003年に開発が始まり，2026年5月に PyMC 6.0 がリリースされた．

## 詳細

### バージョンの変遷

| バージョン | 時期 | 主な変更点 |
|-----------|------|-----------|
| PyMC 3.0 | 2017年1月 | Hamiltonian Monte Carlo（HMC）に対応 |
| PyMC 5.0 | 2022年12月 | バックエンドが Aesara から，それをフォークした PyTensor に刷新 |
| PyMC 6.0 | 2026年5月 | Numba がデフォルトの計算バックエンドに．Nutpie がインストールされていればデフォルトサンプラーとして使用 |

### モデル記述の基本

PyMC では，`with pm.Model()` のコンテキスト内で確率変数（事前分布）と観測データに対する尤度を宣言的に記述する．例えばコイントスの成功確率 $\theta$ を推定するモデルは以下のように書ける:

```python
import pymc as pm

with pm.Model() as model:
    theta = pm.Beta("theta", alpha=1, beta=1)      # 事前分布
    obs = pm.Bernoulli("obs", p=theta, observed=data)  # 尤度
    trace = pm.sample(1000, tune=500)               # NUTS で事後分布をサンプリング
```

モデル定義とサンプリングの実行が明確に分離されており，複雑な階層ベイズモデルも同様の記法で拡張できる．

### サンプラーとバックエンド

PyMC はデフォルトで NUTS（HMC の自動チューニング版）を使用して事後分布をサンプリングする．PyMC 6.0 では，Numba バックエンドと Nutpie（Rust 実装の高速 NUTS サンプラー）の組み合わせにより，従来より高速なサンプリングが可能になった．変分推論（ADVI）による近似推論もサポートしており，大規模データではこちらを選ぶこともできる．

### Stan との比較

| 観点 | PyMC | [[Stan]] |
|------|------|------|
| 実装言語 | Python（内部は PyTensor/Numba） | 専用言語（C++ にコンパイル） |
| モデル記述 | Python コードとして直接記述 | 専用の Stan 言語ファイルを記述 |
| エコシステム | NumPy/pandas/ArviZ 等 Python 生態系との親和性が高い | R・Python・Julia など複数言語からのインターフェースあり |
| 学習コスト | Python に慣れていれば低い | Stan 言語の文法習得が必要 |

## ポイント

- 事前分布・尤度を Python コードとして直感的に記述し，NUTS ベースの MCMC を自動実行する確率的プログラミングライブラリ
- 2026年5月リリースの PyMC 6.0 で Numba バックエンドと Nutpie サンプラーがデフォルト化
- Python エコシステム（NumPy・pandas・ArviZ）との親和性が高く，データサイエンスワークフローに組み込みやすい
- Stan と並ぶベイズモデリングの主要 OSS で，変分推論（ADVI）による高速近似推論もサポート

## 関連項目

- [[ベイズ統計]] — PyMC が実装対象とするモデリングパラダイム
- [[MCMC]] — PyMC が内部で自動実行するサンプリング手法
- [[変分推論]] — PyMC の ADVI による近似推論オプション
- [[Stan]] — 同じく確率的プログラミングを提供する代表的な代替ツール

## 参考

- [PyMC 入門 (1) - Zenn](https://zenn.dev/tmiya/articles/bcc473a450c055)
- [PyMCの中身を覗いてみた - Insight Edge Tech Blog](https://techblog.insightedge.jp/entry/read-pymc-code)
