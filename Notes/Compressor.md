---
title: "Compressor"
date: 2026-03-01
tags:
  - Tool
related:
  - "[[Apple Creator Studio]]"
  - "[[Final Cut Pro]]"
---

## 概要

Compressor は Apple が開発する macOS 向けの業務用動画エンコーディング・書き出しソフトウェア。[[Final Cut Pro]] の強力な書き出しコンパニオンとして位置付けられており、業界標準フォーマットへの詳細なエンコード設定・バッチ処理・配信向け書き出しを担う。2026年1月に Compressor 5.0 がリリースされ、Apple Vision Pro 向けの Immersive Video パッケージ作成に対応した。

## 詳細

### 主要機能

- **高性能エンコーディング**: Metal エンジンを活用して CPU・GPU・メモリを最適分散。Apple シリコンの Apple Media Engine により H.264・HEVC のハードウェアアクセラレーションを実現
- **バッチ処理**: 複数のプロジェクトを異なる書き出し先・フォーマットへ同時並行で処理
- **HDR サポート**: HDR コンテンツを Mac ディスプレイでプレビューしてから書き出し
- **カスタムエンコード設定**: ビットレート・解像度・フレームレート・コーデックを細かく制御

### 対応フォーマット

**映像コーデック:**
- H.264、HEVC（H.265）、ProRes、MPEG-2、AVC-Intra、D-10/IMX、XDCAM HD

**コンテナ:**
- QuickTime、MXF、MOV、MP4、M4V

**特殊対応:**
- 360° VR 動画の書き出し
- 複数フォーマットのクローズドキャプション（SRT・iTT など）
- 音声ディスクリプション（アクセシビリティ）
- カスタム LUT によるカメラログ変換
- 非圧縮 TGA イメージシーケンス・圧縮 TIFF イメージシーケンス

### バージョン履歴（最近）

| バージョン | リリース日 | 主な追加機能 |
|-----------|-----------|------------|
| 4.11 | 2025年9月19日 | ProRes RAW 等の RAW インスペクター（ISO・露出補正・色温度へのアクセス） |
| 5.0 | 2026年1月28日 | Apple Vision Pro 向け Immersive Video パッケージ作成・HLS ストリーミング対応 |

### Apple Vision Pro 対応（Compressor 5.0〜）

Apple Immersive Video パッケージを作成し、Apple Vision Pro での再生や HTTP Live Streaming に対応。空間コンテンツの制作・配信ワークフローを完結できる。

## ポイント

- [[Final Cut Pro]] の書き出しを補完するアプリとして設計されており、Final Cut Pro から直接ジョブを送信できる
- 単体では Final Cut Pro に内蔵されていない高度なエンコード設定（MXF・XDCAM など放送業界向け）が利用可能
- Apple Media Engine（Apple シリコン）により、8K ProRes のエンコードも高速処理
- [[Apple Creator Studio]] サブスクリプション（$12.99/月）に含まれるほか、Mac App Store でも単体購入可能
- macOS 専用（iPad 版・Windows 版なし）

## 関連項目

- [[Apple Creator Studio]]
- [[Final Cut Pro]]

## 参考

- [Compressor - Apple](https://www.apple.com/final-cut-pro/compressor/)
- [Compressor release notes - Apple Support](https://support.apple.com/en-us/102745)
- [Compressor (software) - Wikipedia](https://en.wikipedia.org/wiki/Compressor_(software))
