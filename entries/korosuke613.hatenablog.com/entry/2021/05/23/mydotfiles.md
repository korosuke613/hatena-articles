---
Title: "dotfiles の更新忘れを防いで継続的に管理していく \U0001F527 \U0001F4AA"
Category:
- 生産性向上
- Unix/Linux
Date: 2021-05-25T21:24:33+09:00
URL: https://korosuke613.hatenablog.com/entry/2021/05/23/mydotfiles
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613767287136
---

<!-- ここに導入を書く -->

dotfiles、1 回作って満足しちゃってませんか？変更をコミットし忘れてリモートとローカルの差分が増えて放置している...という人もいるかと思います（僕です）。この記事では dotfiles の更新忘れを防いで継続的に管理していく方法を紹介します。

<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]

<!-- ここに広告が入る -->
---

# dotfiles とは？

dotfiles とはドット(`.`)から始まる設定ファイル群、またはそれらを管理するリポジトリです。`.zshrc`とか `.vimrc` とかですね。必ずしもドット(`.`)からファイル名が始まる必要はないと思います[^dot]。

dotfiles については検索すると色々出てきます。dotfiles 自体の詳細な説明は以下などを参照ください。

- [https://qiita.com/b4b4r07/items/b70178e021bef12cd4a2:title]
- [https://qiita.com/yutkat/items/c6c7584d9795799ee164:title]

[^dot]: 厳密な定義やそもそも誰が提唱したかは調べた限りよくわかりませんでした。

# dotfiles リポジトリの更新を忘れないようにする

## なぜ？

dotfiles リポジトリを作って、`setup.sh` を書いて....と dotfiles をリポジトリで管理するのは多くの人が通ってきた道だと思います。

**しかし dotfiles は作って終わりというものではありません。**

ローカルの dotfiles が常に固定されるのは稀であり、内容は常に変化していくものだと思います。**いつパソコンがおじゃんになってもいいように、リモートリポジトリは常に最新の内容にアップデートしておくべき**です。

実際のところ、僕も 2 年くらい前から dotfiles リポジトリを始めましたが、さっぱり内容を更新していなかったためローカルにある dotfiles とはかけ離れた内容となってしまっていました。

## どうやって？

dotfiles リポジトリを更新しない一番の理由は、**リポジトリの存在を忘れてしまうこと**だと考えました。

各設定ファイルはシンボリックリンクとしてあるべき場所に紐づけられているので、dotfiles が入ったディレクトリの場所を考えなくても容易に編集できてしまいます。これは手軽に編集できるというメリットでもありますが、dotfiles リポジトリの存在を忘れてしまうというデメリットも孕んでいると考えます。

なので、**強制的に dotfiles の存在を気づかせてもらえばいいのではないか**と考えました。

というわけで作ったスクリプトが以下になります。

<script src="https://gist.github.com/korosuke613/4feef9f172c4ee272ed5c70e2b99082a.js?file=check_update_dotfiles.sh"></script>

このスクリプトでは、「『**ステージングしていない**』ことと『**リモートブランチとローカルブランチに差分がある**』ことをチェックして、どちらかが条件を満たせば『**変更をコミットしてプッシュするメッセージを表示する**」ということを行います。

### 「ステージングしていない」ことの確認
5 行目の `git -C "${dotfiles_home}" status --porcelain` では「ステージングしていない」ことを確認しています[^gitc]。

- `--porcelain`は `git status` の結果をマシンリーダブルにするためのオプションです。変更がある場合はそれが標準出力されますが、変更が無い場合は何も出力されません。

[^gitc]: `-C <リポジトリのパス>`とすることで、指定したパスをワーキングディレクトリーとしてくれます。

変更がある場合
```sh
❯ git status --porcelain
 M zsh/.zshrc.check_update_dotfiles

❯ 
```

変更がない場合
```sh
❯ git status --porcelain

❯ 
```

このコマンドを `test -n` の引数として渡すことで、変更があれば `exitcode=0 (=true)`、変更がなければ `exitcode=1 (=false)` とできます。

### 「リモートブランチとローカルブランチに差分がある」ことの確認
6 行目の `git -C ${dotfiles_home} diff --exit-code --stat --cached origin/main` では「リモートブランチ(`origin/main`)とローカルブランチに差分がある」ことを確認しています。

- `--exit-code`は `git diff` 時に差分があると exit code=1 とするオプションです。
- `--stat`は `git diff` の結果を簡略化するためのオプションであり、長々と diff の結果を表示させない、pager[^pager]を使わないように設定しています。
- `--cached`はステージングされた変更、コミットを対象にするオプションです。これによって、push されているかどうかを判別できます。

このコマンドの結果を `!` で反転することで、push されていなければ `exitcode=0 (=true)`、push されていれば `exitcode=1 (=false)`、とできます。

[^pager]: 正直言って`--stat`でpagerが使われないかどうかは未確認です。万全を期すなら`--no-pager`オプションをつけた方がいいかも。

### 「変更をコミットしてプッシュするメッセージを表示する」 
そして、「ステージングしていない」または「リモートブランチとローカルブランチに差分がある」のどちらかを満たしている場合に、7〜16 行目を使って「変更をコミットしてプッシュするメッセージを表示する」ことを行います。


## 結果
ローカルの dotfiles がリモートリポジトリと異なる状態で `check_update_dotfiles.sh` を実行した結果が以下になります。

作ったスクリプトを `~/.zshrc` で呼び出すようにすることで、ターミナルを開くたびにローカルの dotfiles がリモートリポジトリと異なるかどうかを確認してくれます。

もし異なってた場合は**ターミナルを開くたびに以下のように「プッシュしろ！」と怒ってくれる**わけですね。

<figure style="width: 500px;" class="figure-image figure-image-fotolife" title="dotfiles is dirty">[f:id:korosuke613:20210523224623p:plain]<figcaption>dotfiles is dirty</figcaption></figure>


**これでコミット忘れが無くなってリモートの dotfiles を継続的に管理していけそうです💪 💪**


---

# おまけ：ぼくのかんがえたさいきょうの dotfiles
おまけです。僕の dotfiles を紹介します。

以下のリポジトリで管理しています。
[https://github.com/korosuke613/dotfiles:embed:cite]

## 特徴

- セットアップが簡単にできる
  - `setup.sh`を叩くだけで必要なコマンドをインストールして dotfiles のシンボリックリンクを配置してくれる
- dotfiles を更新してもリポジトリが更新されていないと教えてくれる
  - この記事の前半の内容

## ディレクトリ構成
以下のような構成になっています。

<figure style="width: 500px;" class="figure-image figure-image-fotolife" title="tree -a -I &quot;.git&quot; --dirsfirst">[f:id:korosuke613:20210523191801p:plain]<figcaption>tree -a -I &quot;.git&quot; --dirsfirst</figcaption></figure>

それぞれの設定ファイルはルート直下に置くのではなく、コマンド名などでディレクトリを掘ってその中に配置しています。
初期設定に使うファイルはルート直下に配置しています。


## セットアップ用ファイル
初期設定のためのファイル群です。

|ファイル名|説明|
|---|---|
|README.md|セットアップ方法が載ってる|
|Brewfile|Homebrew でインストールしたライブラリが書かれている。`brew bundle`でインストールできる。([Brewfile で Homebrew のライブラリを管理しよう！ - kakakakakku blog](https://kakakakakku.hatenablog.com/entry/2020/09/17/124653))|
|setup.sh|実行するだけで必要なライブラリのインストールと dotfiles のシンボリックリンクを配置してくれる|

### setup.sh
<script src="https://gist.github.com/korosuke613/4feef9f172c4ee272ed5c70e2b99082a.js?file=setup.sh"></script>

`setup.sh`を実行することで、Homebrew がインストールされているか確認、されていなければ Homebrew と Brewfile で指定されたライブラリをインストール、dotfiles のシンボリックリンクを配置を行なってくれます。新しい Mac を買ってもすぐに環境構築できますね。

## 各設定ファイル
各設定ファイルはそれぞれディレクトリに分かれています。

|ディレクトリ名|入ってる設定ファイル|説明|
|---|---|---|
|git|`.gitconfig`, `ignore`[^ignore]|`.gitconfig`には `delta` の設定が入ってる|
|vim|`.vimrc`|あまりカスタマイズしてない|
|zsh|`.zshrc`, `.zshrc.*`|`.zshrc.*`は `.zshrc` から呼び出されるようになっている。|
|[asdf](https://github.com/asdf-vm/asdf)|`.tool_versions`, `.asdfrc`|asdf は様々なランタイムをバージョン管理するツール。anyenv をさらに広くした感じ。インターフェースが統一されているのが良い|
|[starship](https://starship.rs/)|`starship.toml`|starship の設定ファイル。プロンプトに表示するパラメータをカスタマイズできる|

### zsh関連
<script src="https://gist.github.com/korosuke613/4feef9f172c4ee272ed5c70e2b99082a.js?file=.zshrc"></script>

`.zshrc`は適当に管理して内容が膨らみがちなのでこんな感じでファイルを分けて `source` で呼び出すようにしています。この記事で説明した `check_update_dotfiles.sh` もここで呼び出されています。（`.zshrc.check_update_dotfiles`という名前ですが）

管理がしやすくなって良いですね。

[^ignore]: グローバルの gitignore です。`~/.config/git/ignore`に配置する。最近知りました。→ [~/.gitignore_global を指定するのをやめ、デフォルトの置き場に置こう](https://zenn.dev/qnighy/articles/1a756f2857dc20)


<!-- 記事終わり線 -->


<!-- ここに脚注が来る -->
