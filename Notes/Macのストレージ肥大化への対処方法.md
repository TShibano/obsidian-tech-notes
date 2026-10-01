


# Mac ストレージ整理手順

> [!summary] 概要 システム設定のストレージで「書類」が大きいのに、Finderで見ると小さい場合の調査・削除手順。 原因は隠しフォルダ（`~/.xxx`）と `~/Library` 配下の開発ツール・アプリのデータであることが多い。 `[出典]` が無い記述は **一般知識（未検証）**。

## 1. 「書類」の正体

- 「書類」は Documents フォルダだけではなく、ドライブ上のファイル、ダウンロード、ドキュメントなどをまとめたカテゴリ。
    - [出典: MacPaw](https://macpaw.com/ja/how-to/free-up-space-mac)
- Finder の検索（ファイルサイズ条件）は単一の大きなファイルは探せるが、小さなファイルが大量に入った大きなフォルダは分からない。
    - [出典: MacPaw](https://macpaw.com/ja/how-to/find-large-files-mac)

## 2. 調査手順

1. ターミナルでホーム直下の容量を降順に出す。これは基本コマンドなのでよく使う．

```bash
du -h -d 1 ~ 2>/dev/null | sort -hr | head -20
```

3. 大きいフォルダを同じコマンドで掘り下げる（例: `~/Library`, `~/.local`）。

```bash
du -h -d 1 ~/Library 2>/dev/null | sort -hr | head -20
du -h -d 1 ~/Library/Application\ Support 2>/dev/null | sort -hr | head -15
du -h -d 1 ~/Library/Containers 2>/dev/null | sort -hr | head -15
du -h -d 1 ~/Library/Caches 2>/dev/null | sort -hr | head -15
```

> [!note] `du` の注意
> 
> - `du -d 1` はサブフォルダの合計しか出さない。合計がサブフォルダの和より大きければ、その差は**直下のファイル**（仮想ディスクイメージなど）。`ls -lhs` で確認する。
> - `Operation not permitted` は Apple 標準アプリの保護フォルダ。無視してよい。過小評価を避けたいならターミナルにフルディスクアクセスを許可する。
> - 削除後に表示が減らないときは、再起動、APFS のスナップショット（Time Machine）も疑う。
>     - スナップショットが削除済みデータを保持することがある: [出典: Rancher Slack](https://slack-archive.rancher.com/t/16358452/hello-i-ve-a-quick-question-about-the-lima-diffdisk-file-whi)

## 3. 大容量になりやすい場所と対処

|場所|中身|対処|
|---|---|---|
|`~/.ollama`|Ollama のモデル|`ollama ls` → `ollama rm <model>`|
|`~/.lmstudio`|LM Studio のモデル等|`lms ls` かアプリの My Models で確認して削除|
|`~/.local/share/containers/podman/machine/...`|Podman VM のディスクイメージ|4章|
|`~/Library/Containers/com.docker.docker`|Docker Desktop の VM ディスク|5章|
|`~/Library/Application Support/Claude/vm_bundles`|Claude デスクトップのサンドボックスVM|6章|
|`~/Library/Caches/Homebrew`|Homebrew のダウンロードキャッシュ|7章|
|`~/Library/Caches/ms-playwright`|Playwright のブラウザバイナリ|8章|
|`~/Library/Caches/<ブラウザ名>`|ブラウザのキャッシュ|一般に削除可（8章）|
|`~/.cache/huggingface`|Hugging Face のキャッシュ|`hf cache ls` / `hf cache rm ... --dry-run`|
|`~/.cache/uv`|uv のパッケージキャッシュ|`uv cache prune`（9章）|
|`<プロジェクト>/.venv`|uv の仮想環境|完了したプロジェクトのものを削除（9章）|
|`~/Library/Application Support/Steam`|ゲームデータ|Steam 側でアンインストール|

- Ollama / LM Studio / Hugging Face は各ツールのコマンドで消す方が安全。
    - モデルは再ダウンロードが高コスト・不可能な場合があり、ファインチューニング済みモデル、アダプタ、チャット履歴は別に退避する。
    - [出典: Mole](https://mole.fit/blog/how-to-remove-ai-tool-leftovers-mac.md)
- LM Studio はモデルの保存先を変更できる。`~/.lmstudio` だけとは限らない（同上）。
- LM Studio のアプリ本体は約690MBで、大きいのは隠しフォルダ内のモデル。
    - [出典: Nektony](https://nektony.com/how-to/uninstall-lm-studio)

## 4. Podman（macOS）

### 4-1. 中身の整理

```bash
podman ps -a
podman images
podman volume ls
podman system prune --all --volumes
```

- `--all` はコンテナが紐づいていない未使用イメージをすべて削除する。
- ボリュームは既定では削除されず、`--volumes` を付けたときだけ、どのコンテナからも使われていないものが対象になる。
    - [出典: podman docs](https://docs.podman.io/en/latest/markdown/podman-system-prune.1.html)
- 残したいボリュームは先に `podman volume export` で退避する（削除後は戻らない）。

### 4-2. prune してもディスクが縮まないとき

VM のディスクイメージはスパースファイルで、いったん書き込まれたブロックはホスト側に残ることがある。

1. VM 内でトリムを試す（Rancher Desktop では有効と説明されているが、Podman/libkrun での効果は未確認）。
    - [出典: Rancher Slack](https://slack-archive.rancher.com/t/16358452/hello-i-ve-a-quick-question-about-the-lima-diffdisk-file-whi)

```bash
podman machine ssh "sudo fstrim -av"
du -h -d 1 ~/.local/share/containers/podman/machine/libkrun/
```

2. 縮まなければ、VM を作り直すのが確実。
    
    - ファイルを削除して新しいスパースファイルを作る以外に、ホスト側の容量を解放する方法はないと説明されている。
        - [出典: Rancher Slack](https://slack-archive.rancher.com/t/2475393/hello-everyone-i-just-have-a-quick-question-i-ve-tried-to-de)
    - `rm` → `init` → `start` の手順で復旧した例: [containers/podman #21096](https://github.com/containers/podman/issues/21096)

```bash
podman machine stop
podman machine rm      # VM内のイメージ・コンテナ・ボリュームがすべて消える
podman machine init    # 上限を決めるなら --disk-size <GB>（未検証）
podman machine start
```

- Podman 5 の VM ディスクはスパースであるはず、という議論: [podman discussions #17590](https://github.com/podman-container-tools/podman/discussions/17590)

## 5. Docker Desktop の残骸

Docker を使っていない場合、`~/Library/Containers/com.docker.docker/Data` は VM ディスク（`Data/vms/0/data/Docker.raw`）。

- 削除するとローカルのコンテナ、イメージ、名前付きボリュームが消える。バインドマウントしたフォルダは Mac 側にあるので消えない。
    - [出典: Cindori](https://cindori.com/how-to/how-to-uninstall-docker-on-mac)
- アプリが残っていれば Docker 自身のアンインストーラを使う（Troubleshoot → Uninstall）。同上。
- 手動で消す場合の対象:

```bash
rm -rf ~/Library/Containers/com.docker.docker
rm -rf ~/Library/Group\ Containers/group.com.docker
rm -rf ~/Library/Application\ Support/Docker\ Desktop
rm -rf ~/Library/Logs/Docker\ Desktop
```

- [出典: OneUptime](https://oneuptime.com/blog/post/2026-02-08-how-to-completely-uninstall-docker-and-clean-up-all-data/view)
- 同記事には `~/.docker` や `/usr/local/bin/docker` も載っているが、Podman を Docker 互換で使っている場合は中身を確認してから消す。
- イメージやコンテナを消しても仮想ディスクが縮まない問題は Docker でも報告されている（[docker/for-mac #371](https://github.com/docker/for-mac/issues/371)）。

## 6. Claude デスクトップアプリ

- サンドボックス用 VM が `~/Library/Application Support/Claude/vm_bundles/claudevm.bundle` に置かれ、10〜13GB になる事例がある。
    - [出典: claude-code #43390](https://github.com/anthropics/claude-code/issues/43390)
- 仮想ディスク `rootfs.img` は増える一方で、トリムや圧縮はされないという報告もある。
    - [出典: claude-code #65577](https://github.com/anthropics/claude-code/issues/65577)
- 削除手順（Claude を完全に終了してから）:

```bash
rm -rf ~/Library/Application\ Support/Claude/vm_bundles
rm -rf ~/Library/Application\ Support/Claude/Cache
rm -rf ~/Library/Application\ Support/Claude/Code\ Cache
```

- 11GB → 639MB に減った報告: [claude-code #22543](https://github.com/anthropics/claude-code/issues/22543)
- 機能（Cowork、Claude Code のサンドボックス）を使うと再生成され、また大きくなる。残したいセッションデータは `sessiondata.img` などを先にバックアップする。
    - [出典: lilting.ch](https://lilting.ch/en/articles/claude-code-cowork-vm-macos)
- アプリをアンインストールしても VM バンドルは残る。
    - [出典: tw93/Mole #537](https://github.com/tw93/Mole/issues/537)

## 7. Homebrew

- キャッシュは再ダウンロードを避けるためのもの。フォルダを直接消すより `brew cleanup` を使う。
    - [出典: Mole](https://mole.fit/blog/how-to-clear-cache-on-mac.md)

```bash
brew --cache                       # キャッシュの場所
du -sh "$(brew --cache)"
brew cleanup -n                    # プレビュー
brew cleanup                       # 古いバージョンと120日より古いDLを削除
brew cleanup -s                    # 最新版以外のDLも削除（インストール済みのDLは残る）
rm -rf "$(brew --cache)"           # インストール済みのDLも含めて全削除
```

- [出典: Mole](https://mole.fit/blog/how-to-clean-up-homebrew-mac.md)
- [出典: The Wiert Corner](https://wiert.me/2020/10/19/how-to-remove-all-old-and-outdated-brew-packages-on-macos-nixcraft/)
- 不要になった依存関係の整理には `brew autoremove` も使える。

## 8. その他のキャッシュ

- `~/Library/Caches` は一般に削除しても安全（アプリを終了してから、大きいフォルダだけ消すのが無難）。
    - [出典: iBoysoft](https://iboysoft.com/jp/wiki/library-caches-mac.html)
- **ms-playwright**: Playwright が自動でダウンロードしたブラウザ（Chromium / WebKit / Firefox）の置き場。macOS では `~/Library/Caches/ms-playwright`。各数百MB。古いバージョンは更新時に自動整理される。
    - [出典: Playwright docs](https://github.com/p0x6/playwright/blob/master/docs/installation.md)
    - ライブラリとブラウザ本体は別配布。消しても `playwright install` で再取得できる。
    - [出典: invisible_playwright wiki](https://github.com/feder-cr/invisible_playwright/wiki/playwright-executable-doesnt-exist)
- **使っていないブラウザ（Arc、Floorp など）**: `Caches` 配下は削除可。アプリごと消すなら `~/Library/Application Support/<アプリ>` も整理できるが、ブックマークやログイン情報が入っている場合があるので先にエクスポートする。

## 9. uv（Python）の仮想環境とキャッシュ

### 9-1. 削除してよい根拠

- `.venv` はプロジェクトのファイル（`pyproject.toml` / `uv.lock`）から生成されるもので、削除しても再作成できる。`uv sync --locked` で再作成する。
    - [出典: Kempner Institute Handbook](https://handbook.eng.kempnerinstitute.harvard.edu/s1_high_performance_computing/development_and_runtime_envs/using_uv_env.html)
- 誤って消しても、ロックファイルを基に `uv sync` で戻せる。
    - [出典: Deepnote](https://deepnote.com/blog/ultimate-guide-to-uv-library-in-python)

> [!warning] 削除前に確認
> 
> - `uv.lock` と `pyproject.toml` がコミット済みか（ロックが無いと同じ環境を再現できない）。
> - `uv pip install` などでロック外に手で入れたパッケージは、再作成で失われる（一般知識）。
> - 社内ミラー経由やオフライン環境では、再作成時にパッケージを取得できるか確認する（一般知識）。
> - エディタや Jupyter が `.venv` の Python を使っていない状態で消す。使用中だと削除に失敗し、残骸で再作成できなくなる例がある（Windows での報告）。
>     - [出典: astral-sh/uv #13986](https://github.com/astral-sh/uv/issues/13986)

### 9-2. 完了したプロジェクトの `.venv` を探す

```bash
# .venv を大きい順に（Library / .local / .cache は除外。ホーム全体を走査するので時間がかかる）
find ~ \( -path ~/Library -o -path ~/.local -o -path ~/.cache \) -prune -o \
  -type d -name .venv -prune -exec du -sh {} + 2>/dev/null | sort -hr | head -30

# 参考: 90日以上更新されていない uv プロジェクト（uv.lock の更新日で判定する目安）
find ~/projects -maxdepth 3 -name uv.lock -mtime +90 -exec dirname {} \;
```

- 更新日は最終作業日とは限らない。一覧を見て、完了した/使わないプロジェクトだけ選ぶ。
- `find` のコマンドは一般知識（未検証）。`~/projects` は自分の作業ディレクトリに置き換える。

### 9-3. 削除と復旧

```bash
cd <project>
rm -rf .venv

# 再開するとき
uv sync --locked
```

### 9-4. uv のキャッシュ

- 既定のキャッシュ場所は、macOS / Linux で `~/.cache/uv`。`uv cache dir` で確認できる。
    - [出典: pydevtools](https://pydevtools.com/handbook/how-to/how-to-manage-uv-cache-size/)

```bash
uv cache dir
du -sh "$(uv cache dir)"
uv cache prune     # 未使用のエントリだけ削除（定期実行しても安全）
uv cache clean     # キャッシュを全削除（次回の sync で再ダウンロード）
```

- `uv cache prune` は未使用のキャッシュと、集中管理されたプロジェクト環境を削除する。定期実行しても安全。
    - [出典: uv docs](https://docs.astral.sh/uv/concepts/cache/)
- 実例: `~/.cache/uv` が63.4GBになり、`uv cache prune` で37.3GiB を回収。
    - [出典: Simon Willison](https://simonwillison.net/2025/Jul/8/uv-cache-prune/)
- キャッシュフォルダの中身は手で消さない。`uv cache clean` / `uv cache prune` を使う。
    - [出典: pydevtools](https://pydevtools.com/handbook/how-to/how-to-manage-uv-cache-size/)
- パッケージ単位なら `uv cache clean <パッケージ名>`（[uv docs](https://docs.astral.sh/uv/concepts/cache/)）。

> [!note] 容量の見え方 uv はキャッシュから環境へ、ハードリンクや copy-on-write のクローンでファイルを配置する。そのため `du` の値と実際に解放される容量がずれることがある。 `uv cache clean --preview-features cache-physical-space` を使うと、ハードリンクとクローンを考慮した、より正確な見積もりが得られる（macOS / Linux 対応）。
> 
> - [出典: uv docs](https://docs.astral.sh/uv/concepts/cache/)
> 
> macOS（APFS）の既定がクローンであること、その結果として `.venv` を消しても、キャッシュ側が残っていると空き容量があまり増えない場合があることは、私の一般知識（未検証）。 効果を確認するには、`.venv` を消した後に `uv cache prune` も実行して、ストレージの空きを比べる。

### 9-5. uv が管理するその他のもの

一般知識（未検証）:

```bash
uv python list --only-installed   # uv が入れた Python 本体
uv python uninstall <version>     # 使わない Python を削除
uv tool list                      # uv tool / uvx で入れたツール
uv tool uninstall <name>
```

- `uv tool` のツールも、それぞれ独立した仮想環境として容量を使う。

## 10. チェックリスト

- [ ] `du -h -d 1 ~` で大きいフォルダを特定した
- [ ] 隠しフォルダ（`~/.xxx`）と `~/Library` を掘り下げた
- [ ] ツール（Ollama / Podman / Docker / LM Studio）は、そのツールのコマンドかアンインストーラで消した
- [ ] 残したいボリューム・モデル・セッションデータを退避した
- [ ] 完了した uv プロジェクトの `.venv` を削除し、`uv cache prune` も実行した
- [ ] 削除後にゴミ箱を空にした
- [ ] 表示が減らなければ再起動し、APFS スナップショットも確認した