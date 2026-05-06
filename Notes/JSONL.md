---
title: "JSONL"
date: 2026-05-06
tags:
  - Tool
  - DB
related:
  - "[[JSON]]"
  - "[[データフォーマット]]"
  - "[[CSV]]"
---

## 概要

JSONL（JSON Lines，ジェイソンエル）は1行1レコードの形式でJSONオブジェクトを並べたデータフォーマット．ストリーミング処理や機械学習のデータセットに適しており，大量データを逐次処理するユースケースで広く使われる．

## 詳細

### フォーマット仕様

```jsonl
{"id": 1, "name": "Alice", "age": 30}
{"id": 2, "name": "Bob", "age": 25}
{"id": 3, "name": "Charlie", "age": 35}
```

- 各行が独立した有効な JSON オブジェクト
- 行区切り: `\n`（LF）
- 文字コード: UTF-8
- 拡張子: `.jsonl`（`.ndjson` とも呼ばれる）

### JSONとの比較

| 項目 | JSON | JSONL |
|------|------|-------|
| 構造 | 1ファイル=1JSON | 1行=1レコード |
| 部分読み込み | 不可（全体をパース） | 可（行単位でストリーム処理） |
| 追記 | 困難（構造を壊す） | 容易（末尾に1行追加） |
| メモリ効率 | 大規模データは非効率 | 大規模データでも省メモリ |
| 可読性 | ツリー構造で見やすい | 1行が長くなりがち |

### 主な用途

- **LLM ファインチューニング**: 学習データを JSONL 形式で提供（OpenAI, Azure ML 等）
- **ログ収集**: 構造化ログの保存（各ログエントリが1行）
- **ETL パイプライン**: 中間データフォーマットとしてストリーミング処理
- **データセット配布**: HuggingFace Dataset 等でも採用

## ポイント

- JSON は「木構造データの表現」，JSONL は「レコード列のストリーム」という使い分け
- Spark・Pandas・DuckDB など主要ツールが JSONL を直接サポート
- ファイルが壊れていても，エラー行以降のレコードは読み込める（行独立性）

## 関連項目

- [[JSON]]
- [[データフォーマット]]
- [[CSV]]
- [[XML]]

## 参考

- [JSON と JSONL](https://zenn.dev/takagi_tech/articles/programming-format-json-vs-jsonl-full-guide)
- [コンピューター ビジョン タスク用の JSONL 形式 - Azure Machine Learning](https://learn.microsoft.com/ja-jp/azure/machine-learning/reference-automl-images-schema?view=azureml-api-2)
- [ちょっとしたデータファイルのフォーマットにJSON Linesを使うと便利](https://blog.3qe.us/entry/2022/08/22/131127)
