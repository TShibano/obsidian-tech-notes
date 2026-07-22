

# セットアップ手順

## USBメディアの準備
1. USBを用意する(8GBでも足りそう．16GBあると安心)
2. [公式サイト](https://endeavouros.com)よりISOファイルをダウンロードする
3. SHA512sumでハッシュ検証を行う．
	1. `sha512sum endeavouros-*.iso` で出力された結果と公式サイトのハッシュ値を検証する
4. GPGSignature(電子署名)を確認する．詳細は[こちら](https://discovery.endeavouros.com/signature-and-keyring/how-to-check-and-trust-key-and-signature-for-the-endeavouros-iso/2025/01/)
	1. `gpg --keyserver keyserver.ubuntu.com --recv-keys CDF595A1` 
	2. `gpg --edit-key CDF595A1`
		1. trust -> 5 で信頼する
	3. `gpg --verify endeavouros-*.iso.sig endeavouros-*.iso`
5. MacOSでは，BalenaEtcherを使って，USBにisoファイルを書き込む

## インストール
このインストール方法はデュアルブートではなく，クリーンインストールする方法である．

1. インストール先のPC本体のBIOSで以下の設定を行う
	1. セキュアブートを無効にする
	2. 起動順で，USBを先頭にする
2. 起動後，EndeavourOSの指示に従ってインストールする．以下気になるところだけピックアップ．
	1. パーティションの設定
		1. swapファイルを作成する．
		2. ハイバーネートは不要
			1. ハイバーネートとは，PCの状態をディスクに保存して電源を切る機能
	2. デスクトップ環境 -> i3-wmを選択
		1. 何を選んでも良いが，気になったものを整理．
		2. KDE Plasma: フルDEでリッチ．
		3. Xfce: 軽量でシンプル
		4. i3-wm: 非常に軽量．タイル型マネージャーという他と少し変わった方法

## 初期設定
今後行う．
i3-wmは早めに慣れておきたい．