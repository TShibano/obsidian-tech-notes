---
title: "SSO"
date: 2026-05-06
tags:
  - Security
  - Auth
related:
  - "[[ゼロトラストセキュリティ]]"
  - "[[API]]"
---

## 概要

SSO（Single Sign-On，シングルサインオン）は，1つのIDとパスワードで複数のサービスにログインできる認証の仕組み．エンタープライズ環境でのユーザー管理とセキュリティを両立させる標準的なアプローチ．

## 詳細

### 主なプロトコル

| プロトコル | 用途 | 特徴 |
|-----------|------|------|
| SAML 2.0 | エンタープライズ SSO | XML ベース，IdP ↔ SP 間でアサーションを交換 |
| OAuth 2.0 | 認可（Authorization） | リソースへのアクセス委譲．認証機能はなし |
| OIDC | 認証 + 認可 | OAuth 2.0 を拡張し認証を追加．IDトークン（JWT）を発行 |

### 動作フロー（SAML）

1. ユーザーがサービスプロバイダ（SP）にアクセス
2. SP が Identity Provider（IdP）にリダイレクト
3. IdP で認証（ログイン）
4. IdP が署名付き SAML アサーションを SP に返す
5. SP がアサーションを検証してアクセス許可

### SAMLとOIDCの使い分け

- **SAML**: 既存エンタープライズ基盤（Active Directory など）との連携
- **OIDC**: Web アプリ・モバイルアプリ向けの現代的なフロー

## ポイント

- OAuth 2.0 は「認可」のみで「認証」は行わない（混同しやすい）
- OIDC = OAuth 2.0 + 認証レイヤー
- SSO のセキュリティリスク: IdP が侵害されると全サービスが危険になる
- SAML はデジタル署名でアサーションの改ざんを防止

## 関連項目

- [[ゼロトラストセキュリティ]]
- [[API]]

## 参考

- [SSOの仕組みと認証方式を図解で解説](https://www.identification-valley.net/single-sign-on/structure-and-methods.html)
- [SAML認証とは？シングルサインオン（SSO）を実現する仕組み](https://eset-info.canon-its.jp/malware_info/special/detail/240227.html)
- [シングルサインオンを実現する際に利用する「SAML」「OIDC」「OAuth」プロトコルとは？](https://service.appunity.jp/id/blog/explanation_protocol-sso)
