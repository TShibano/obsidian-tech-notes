---
title: "Julia"
date: 2026-02-23
tags:
  - Language
  - AI
  - ML
related:
  - "[[Rust]]"
  - "[[RAG]]"
  - "[[データ指向プログラミング]]"
  - "[[Pluto]]"
---

## 概要

Julia は科学技術計算に特化した高性能な汎用プログラミング言語。「Python の使いやすさと C の速度を両立する」ことを目標に設計され、LLVM による JIT コンパイルと多重ディスパッチにより動的言語でありながら C・Fortran に匹敵するパフォーマンスを実現する。機械学習・数値解析・最適化分野で採用が広がっている。

## 詳細

### 基本情報

| 項目 | 内容 |
|------|------|
| 開発者 | Jeff Bezanson, Stefan Karpinski, Viral B. Shah, Alan Edelman（MIT） |
| 初版 | 2012年（1.0: 2018年8月） |
| 最新版 | 1.12.5（2026年2月リリース） |
| コンパイラ | LLVM JIT |
| ライセンス | MIT |
| 公式サイト | julialang.org |

### 核心的な設計思想

#### 二言語問題の解決

従来の科学技術計算では、プロトタイピングに Python/MATLAB を使い、性能が必要な部分は C/Fortran で書き直すという「二言語問題」が存在した。Julia は単一言語で高水準なコードと高性能を両立し、この問題を解消する。

#### 多重ディスパッチ（Multiple Dispatch）

Julia の中核パラダイム。関数の呼び出しは引数の**すべての**型の組み合わせに基づいて最適なメソッドを選択する。

```julia
# 多重ディスパッチの例
function area(shape::Circle)
    π * shape.radius^2
end

function area(shape::Rectangle)
    shape.width * shape.height
end

# Julia が型を見て自動的に適切なメソッドを呼ぶ
area(Circle(5.0))      # → 78.539...
area(Rectangle(3, 4))  # → 12
```

これにより、型の特殊化（Type Specialization）が行われ、コンパイラが高度に最適化されたコードを生成できる。

#### 型安定性とパフォーマンス

Julia の速度の鍵は「型安定性（Type Stability）」。関数内のすべての変数の型がコンパイル時に推論できる場合、コンパイラは C と同等のコードを生成する。

```julia
# 型安定な関数（高速）
function sum_squares(n::Int)
    s = 0.0  # Float64 として初期化
    for i in 1:n
        s += i^2
    end
    return s
end
```

### 主要な言語機能

#### REPL と対話的開発

```julia
# Julia REPL
julia> x = [1, 2, 3, 4, 5]
julia> using Statistics
julia> mean(x)
3.0
```

#### ブロードキャスト（vectorization）

```julia
# ドット記法でベクトル演算を簡単に
x = [1, 2, 3, 4, 5]
y = x .^ 2 .+ 2 .* x   # 要素ごとの演算
```

#### 並列処理

```julia
# マルチスレッド
Threads.@threads for i in 1:1000
    # 並列処理
end

# GPU 計算（CUDA.jl）
using CUDA
x_gpu = CuArray([1.0, 2.0, 3.0])
```

### 科学技術エコシステム

| パッケージ | 分野 | 説明 |
|-----------|------|------|
| DifferentialEquations.jl | 数値解析 | 微分方程式ソルバー（世界最高水準） |
| Flux.jl | ML | Julia ネイティブの機械学習フレームワーク |
| JuMP.jl | 最適化 | 数理最適化のモデリング言語 |
| Plots.jl | 可視化 | 統一 API で複数バックエンドに対応 |
| DataFrames.jl | データ操作 | pandas 相当の DataFrame ライブラリ |
| Turing.jl | ベイズ推定 | 確率的プログラミング |
| LinearAlgebra | 線形代数 | 標準ライブラリ（BLAS/LAPACK ラッパー） |

### ドメイン特化エコシステム

| ドメイン | 組織 |
|---------|------|
| 生物学 | BioJulia |
| 経済学 | QuantEcon |
| 天文学 | JuliaAstro |
| 量子物理 | QuantumBFS |
| 画像処理 | JuliaImages |
| 非線形ダイナミクス | JuliaDynamics |

### 最新動向（2025〜2026年）

#### Julia 1.12（2025年10月）

- **juliac コンパイラ**: `JuliaC.jl` パッケージで AOT コンパイルが可能になり、小さなスタンドアロンバイナリを生成できるようになった
- Google Colab が Julia をネイティブサポート開始（2025年）

#### パフォーマンス比較（参考）

Julia は多くのベンチマークで Python の 10〜100 倍、場合によっては C に近い性能を示す。特に数値計算・線形代数・微分方程式ソルバーでの優位性が大きい。

### Python・R との比較

| 観点 | Julia | Python | R |
|------|-------|--------|---|
| 速度 | ◎（C 並み） | △（インタープリタ） | △ |
| 数値計算 | ◎ | ○（NumPy 経由） | ○ |
| ML エコシステム | △ | ◎（PyTorch等） | △ |
| 統計 | ○ | ○ | ◎ |
| 起動時間 | △（JIT コンパイル） | ○ | ○ |
| コミュニティ規模 | △ | ◎ | ○ |

## ポイント

- 科学技術計算の「二言語問題」を解決：プロトタイプと本番を同一コードで
- 多重ディスパッチ + 型特殊化による C 並みのパフォーマンスを動的言語で実現
- DifferentialEquations.jl は世界最高水準の微分方程式ソルバーエコシステム
- Julia 1.12 の juliac で AOT コンパイルが可能になり、デプロイも改善
- Google Colab ネイティブサポートでアクセスが向上

## 関連項目

- [[Rust]] - 同じく高性能を目指す言語（システムプログラミング向け）
- [[RAG]] - Julia の機械学習エコシステムとの関連
- [[データ指向プログラミング]] - データ処理・科学技術計算においてデータ指向的な設計が活きる
- [[Pluto]] - Julia 専用のリアクティブノートブック環境

## 参考

- [Julia 公式サイト](https://julialang.org/)
- [Julia Documentation](https://docs.julialang.org/en/v1/)
- [Julia Wikipedia](https://en.wikipedia.org/wiki/Julia_(programming_language))
- [Julia Performance Tips](https://docs.julialang.org/en/v1/manual/performance-tips/)
- [JuliaCon](https://juliacon.org/)
