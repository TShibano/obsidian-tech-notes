---
title: "shellcheck"
date: 2026-09-07
tags:
  - Tool
  - CLI
related:
  - "[[Haskell]]"
---

## 概要

ShellCheck は，シェルスクリプト（sh / bash / dash / ksh）を静的解析し，構文エラー・移植性の問題・危険なパターン・典型的なミスを検出するツール．スクリプトを実行せずに解析するため，本番投入前の安全確認や CI/CD パイプラインへの組み込みに適している．

## 詳細

### 検出内容

初心者が陥りがちな構文ミスから，シェルが「一見動くが予期しない挙動をする」中級レベルの意味的な問題，さらに将来的な環境変化で壊れうる高度なコーナーケースまで，幅広いレベルの警告・提案を出す．

### 対応シェル

Bourne shell (sh)，Bourne Again shell (bash)，Debian Almquist shell (dash)，Korn shell (ksh) など複数のシェル方言に対応している．

### 利用方法

- **オンライン**: [shellcheck.net](https://www.shellcheck.net/) にスクリプトを貼り付けるだけで即座にフィードバックを得られる（常に最新の git commit に同期）
- **エディタ統合**: VSCode（vscode-shellcheck），Sublime（SublimeLinter），Pulsar/Atom など主要エディタ向けの拡張機能が存在
- **CI/CD 組み込み**: 標準的な終了コードを返すため，ビルドやテストスイートの一部としてそのまま組み込める

### 実装

Haskell で実装されたオープンソースツールであり，GitHub（koalaman/shellcheck）で開発が続けられている．

## ポイント

- シェルスクリプトを**実行せずに**静的解析し，構文・移植性・意味的な問題を検出する
- sh / bash / dash / ksh など複数の方言に対応
- 終了コードが標準化されており，CI/CD パイプラインに組み込みやすい
- Haskell 製で，[shellcheck.net](https://www.shellcheck.net/) からブラウザ上でも試せる

## 関連項目

- [[Haskell]] — ShellCheck の実装言語

## 参考

- [ShellCheck – shell script analysis tool](https://www.shellcheck.net/)
- [GitHub - koalaman/shellcheck](https://github.com/koalaman/shellcheck)
