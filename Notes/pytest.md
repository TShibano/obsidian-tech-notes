---
title: "pytest"
date: 2026-11-09
tags:
  - Language
  - Python
  - Tool
  - CLI
related:
  - "[[Python]]"
  - "[[uv]]"
  - "[[ruff]]"
  - "[[ty]]"
  - "[[Docker]]"
  - "[[Git]]"
---

## 概要

pytest は Python のテストフレームワークで，素の `assert` 文によるテスト記述，DI 風の fixture，`parametrize`，豊富なプラグインを特徴とする．標準ライブラリの `unittest` で書かれたテストもそのまま実行できるため，段階的に移行できる．2026-11-09 時点の最新版は 9.2.0（2026-10-27 リリース）．

## 詳細

### unittest との違い

| 観点 | unittest | pytest |
|------|----------|--------|
| テスト記述 | `TestCase` を継承したクラス + `assertEqual` 等のメソッド | 関数 + 素の `assert`（assertion rewriting で差分を詳細表示） |
| セットアップ | `setUp` / `tearDown` | fixture（スコープ指定，依存の連鎖，`yield` による後始末） |
| パラメータ化 | 標準では `subTest` が近い | `@pytest.mark.parametrize` が組み込み |
| 拡張 | 標準ライブラリのみ | プラグイン（xdist，cov，asyncio など） |
| 互換性 | - | unittest のテストをそのまま実行可能 |

### fixture

- `@pytest.fixture` を付けた関数で，テストが引数名で要求する．fixture 同士も依存できる．
- スコープは function / class / module / package / session．
- `yield` より前がセットアップ，後ろがティアダウン．
- `autouse=True` で要求なしに自動適用．`conftest.py` に置くとディレクトリ配下で共有できる．
- fixture が例外を出すとそのテストは failed ではなく error になる．
- SIGTERM / SIGQUIT ではクリーンアップが実行されない（外部リソースを扱う fixture は漏れに注意）．

```python
import pytest

@pytest.fixture(scope="module")
def db():
    conn = connect()
    yield conn
    conn.close()

def test_query(db):
    assert db.query("select 1") == 1
```

### parametrize

```python
@pytest.mark.parametrize(
    "test_input,expected",
    [("3+5", 8), pytest.param("6*9", 42, marks=pytest.mark.xfail)],
)
def test_eval(test_input, expected):
    assert eval(test_input) == expected
```

- 個別ケースへのマークは `pytest.param(..., marks=...)`．
- デコレータを重ねると全組み合わせ（直積）になる．クラスや `pytestmark` でも適用できる．
- 動的に作る場合は `pytest_generate_tests` フックで `metafunc.parametrize()` を呼ぶ．
- パラメータ値はコピーされずそのまま渡るため，可変オブジェクトの変更は後続ケースに影響する．
- 9.2.0 から `indirect` / `ids` / `scope` はキーワード専用引数になった．

### 設定

- 優先順位: `pytest.toml` / `.pytest.toml` > `pytest.ini` / `.pytest.ini` > `pyproject.toml` > `tox.ini` > `setup.cfg`．最初に見つかった 1 ファイルのみ使われ，マージされない．
- 9.0 以降は `pyproject.toml` のネイティブ TOML テーブル `[tool.pytest]` が使える（従来の `[tool.pytest.ini_options]` とは併用不可）．

```toml
[tool.pytest]
minversion = "9.0"
addopts = ["-v"]
testpaths = ["tests"]
```

### 主なプラグイン

（以下は既存知識および比較記事に基づく．個別プラグインの最新版は未確認）

- pytest-xdist: 並列実行（`pytest -n auto`）
- pytest-cov: カバレッジ計測（`pytest --cov`）
- pytest-asyncio: 非同期テスト
- 自作プラグインは `conftest.py` や entry point で提供できる．

### 最新動向（2026-11-09 時点）

| バージョン | 日付 | 主な内容 |
|-----------|------|----------|
| 9.0.0 | 2025-11 | Python 3.9 サポート終了，subtests（実験的），ネイティブ TOML 設定，`strict` オプション，`PytestRemovedIn9Warning` がデフォルトでエラー |
| 9.1.0 | 2026-06-13 | Python 3.15 サポート，`pytest.register_fixture()`，`--max-warnings`，`approx()` が datetime / timedelta 対応，複数の非推奨化 |
| 9.1.1 | 2026-06-19 | バグ修正 |
| 9.2.0 | 2026-10-27 | `parametrize_long_str_id_strategy`，`-m` 式のマーカー名検証（`strict_markers` 時），プラグイン import 失敗時の終了コード整理，`ParameterSet` / `Approx` の公開，非推奨化（`yield_fixture`，インスタンスメソッドとして定義した上位スコープ fixture など） |

- 9.1.0 で非推奨になり次期メジャーで削除予定: インスタンスメソッドのクラススコープ fixture，ティアダウン中の `request.getfixturevalue()`，ジェネレータ等を `argvalues` に渡すこと，`--pastebin`，`pytest.console_main()`．
- subtests は parametrize と違い，値が収集時に決まらない場合にも使える（`subtests.test()` / `unittest.TestCase.subTest()`）．

## ポイント

- 新規は `assert` + fixture + parametrize で書き，unittest 資産はそのまま流用できる．
- 設定は pytest 9 以降なら `[tool.pytest]` のネイティブ TOML に寄せる．
- `strict` を有効にすると，マーカー綴りミスや ID 重複を早期検出できる．
- メジャー更新前に `PytestRemovedIn9Warning` 等の警告を潰しておくと移行が楽．
- 並列実行やカバレッジはプラグインで補う．

## 関連項目

- [[Python]]
- [[uv]]
- [[ruff]]
- [[ty]]
- [[Docker]]
- [[Git]]

## 参考

- [pytest changelog](https://docs.pytest.org/en/stable/changelog.html)
- [About fixtures](https://docs.pytest.org/en/stable/explanation/fixtures.html)
- [How to parametrize fixtures and test functions](https://docs.pytest.org/en/stable/how-to/parametrize.html)
- [Configuration](https://docs.pytest.org/en/stable/reference/customize.html)
