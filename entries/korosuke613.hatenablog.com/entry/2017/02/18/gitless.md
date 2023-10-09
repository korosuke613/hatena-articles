---
Title: gitをシンプルでわかりやすくするツールGitlessの紹介[和訳]
Category:
- アーカイブ
- プログラミング
Date: 2017-02-18T00:00:00+09:00
URL: https://korosuke613.hatenablog.com/entry/2017/02/18/gitless
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613632476418
---

<!-- ここに導入を書く -->
**この記事は、[https://qiita.com/Shitimi_613/items/8b25de2c586cc85737af:title]のアーカイブです。**

gitについていろいろ調べていたら、Gitlessというgitのわかりにくい部分をシンプルにするツールを見つけましたが、日本語ドキュメントが現在(2017/02/19)さっぱりなかったので、勉強のために[公式サイト](http://gitless.com/)の紹介ページを和訳してみました。

Google翻訳を多用しており、残りは意訳しています。Gitless自体理解しきれていないためよくわからずに訳した部分もあります。それは違うよ！という部分がありましたらコメントにてご指摘いただけると幸いです。

※ [Gitless vs. Git](#gitless_vs_git)の比較画像はうまい載せ方を見つけられずに翻訳できていません。ご容赦くださいm(_ _)m
※ 本文に出てくる実行画面の部分は訳さずに載せています。<＊数字>は原文にはありません。

<!--  カスタムURLは`YYYY/MM/DD/name`の形式にする -->
<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]

---


#About
Gitlessは、Git上に構築されたバージョン管理システムです。
多くの人がGitは使いにくいと訴えています。私たちは、Gitの基本的なシステムの中で、ユーザインタフェースよりも深い場所に問題はあると考えています。
Gitlessは、基本的なシステムを変更するベニア板をアプリケーション上に置くとどうなるかを調べるための実験です。GitlessはGit上に実装されているので(いわゆるGitのプロが「磁器」と呼ぶものです)、いつでもGitに戻ることができます。
もちろん、あなたとリポジトリを共有している同僚が、あなたがGitの愛好家ではないことを知ることは決してありません。

Gitlessを開始するには、[Documentation](#Documentation)をチェックしてください。

* バージョン管理を初めてお使いの方は、[Documentation](#Documentation)を十分に活用して始めてください。
* あなたが愛するGitとは何が違うのかを見たいGitのプロの方は、[Gitless vs. Git](#gitless_vs_git)で違いを見つけることができます。
* ソフトウェアデザインに興味があり、Gitlessの研究についてもっと学びたい場合は、[Research](#Research)を参照してください。

<a name="Documentation"></a>
#Documentation
目次：[Interface](#Interface)、[リポジトリの作成](#create_repo)、[コミット](#save_changed)、[ブランチ](#branch)、[タグ付け](#tag)、[リモートリポジトリの操作](#remote)

<a name="Interface"></a>
##Interface

* [`gl init`](#gl_init) - 空のリポジトリを作成、または既存のリモートリポジトリからリポジトリを作成する
* [`gl status`](#gl_status) - リポジトリのステータスを表示する
* [`gl track`](#gl_track) - ファイルへの変更の追跡を開始する
* [`gl untrack`](#gl_untrack) - ファイルへの変更の追跡を停止する
* [`gl diff`](#gl_diff) - ファイルへの変更の比較を表示する
* [`gl commit`](#gl_commit) - ローカルリポジトリの変更を記録する
* [`gl checkout`](#gl_checkout) - コミットされたバージョンのファイルにチェックアウトする
* [`gl history`](#gl_history) - コミット履歴を表示する
* [`gl branch`](#gl_branch) - ブランチの一覧表示、作成、編集または削除をする
* [`gl switch`](#gl_switch) - ブランチを切り替える
* [`gl tag`](#gl_tag) - タグの一覧表示、作成または削除をする
* [`gl merge`](#gl_merge) - あるブランチの変更を別のブランチにマージする
* [`gl fuse`](#gl_fuse) - あるブランチの変更を別のブランチにヒューズする
* [`gl resolve`](#gl_resolve) - コンフリクトを解決したファイルにマークを付ける
* [`gl publish`](#gl_publish) - アップストリームにコミットを公開する
* [`gl remote`](#gl_remote) - リモートの一覧表示、作成、編集または削除をする

<a name="create_repo"></a>
##リポジトリの作成
"foo"ディレクトリをリポジトリにしたいとします。これは、<a name="gl_init"></a>`gl init`コマンドで行います。これにより、現在の作業ディレクトリが空のリポジトリに変換され、`foo`内ファイルの変更の保存を開始する準備が整いました。

```bash:リポジトリの作成
$ mkdir foo
$ cd foo/
$ gl init
✔ Local repo created in /MyFiles/foo *1
```

*1 ローカルリポジトリ`/MyFiles/foo`を作成しました

ほとんどの場合、空のリポジトリで作業を始めるのではなく、既存のリモートリポジトリをクローンして作業をしたいでしょう。
リモートリポジトリをクローンをローカルに作成するには、同じ`gl init`コマンドの後にリポジトリのURLを与えることで実現できます。

```bash:リモートリポジトリをクローン
$ mkdir experiment
$ cd experiment/
$ gl init https://github.com/spderosso/experiment
✔ Local repo created in /MyFiles/experiment *2
✔ Initialized from remote https://github.com/spderosso/experiment *3
```

*2 ローカルリポジトリ`/MyFiles/experiment`を作成しました
*3 リモートリポジトリ`https://github.com/spderosso/experiment`から初期化しました

<a name="save_changed"></a>
##コミット
これでローカルリポジトリが完成しましたので、ファイルへの変更の保存を開始しましょう。Gitlessのファイルは、 *tracked*、*untracked*、または*ignore*することができます。

* *tracked file*は、Gitlessが変更を検出するファイルです。追跡されたファイルは変更があった場合、自動的にコミットの対象になり(gitで言うadd)、ステータスの「Tracked files with modifications」セクションに表示されます。
* *untracked file*は、Gitlessが変更を検出しないファイルです。これらは自動的にはコミットの対象にはならず、ステータスの「Untracked files」セクションに表示されます。
* *ignore file*は、Gitlessによって完全に無視されるファイルであり、ステータスの出力に現れません。

<a name="gl_status"></a>`gl status`コマンドの出力例です。`foo.py`と`bar.py`は変更が加えられた*tracked file*、`.gitignore`は変更が加えられていない*tracked file*、`baz.py`は*untracked file*、`foo.pyc`は*ignore file* です

```bash:ステータスの確認
$ ls
bar.py  baz.py  foo.py  foo.pyc .gitignore
$ gl status
On branch master, repo-directory //

Tracked files with modifications: *4
  ➜ these will be automatically considered for commit
  ➜ use gl untrack <f> if you don't want to track changes to file f
  ➜ if file f was committed before, use gl checkout <f> to discard local changes

    foo.py
    bar.py

Untracked files: *5
  ➜ these won't be considered for commit)
  ➜ use gl track <f> if you want to track changes to file f

    baz.py
```


```bash:*4
変更が加えられた追跡ファイル：
  ➜ これらは自動的にコミットの対象になります
  ➜ ファイル<f>の変更を追跡したくない場合は、gl untrack <f>を使用します
  ➜ 以前にファイル<f>がコミットされている場合は、gl checkout <f>を使用してローカル変更を破棄します
```

```bash:*5
追跡されていないファイル：
  ➜ これらはコミットの対象にはなりません
  ➜ ファイル<f>の変更を追跡する場合は、gl track <f>を使用します
```

さて、これらの3つの異なる状態の間でファイルはどのように動くのでしょう？

.gitignoreファイルに記述されているignore指定と一致するファイルは無視されます。上記の例では、内容が `*.pyc`の.gitignoreファイルがあります。`foo.pyc`はそのパターンと一致しているので無視されます。

ignore specでマッチしない新しいファイルは、最初は*untracked file*です。あなたがそれを追跡したいならば、<a name="gl_track"></a>`gl track`コマンドでそれを行うことができます。<a name="gl_untrack"></a>`gl untrack`コマンドを使用すると、追跡されたファイルに対する変更の追跡を停止することができます。<a name="gl_checkout"></a>`gl checkout`コマンドを使用すると、ファイルを以前のバージョンにいつでも戻すことができます。

```bash:ファイルの追跡・非追跡の変更とチェックアウト
$ gl track baz.py
✔ File baz.py is now a tracked file *6
$ gl track baz.py
✘ File baz.py is already a tracked file *7
$ gl untrack baz.py
✔ File baz.py is now an untracked file *8
$ gl checkout foo.py
You have uncommitted changes in foo.py that would be overwritten by checkout. Do you wish to continue? (y/N) *9
> y
✔ File foo.py checked out successfully to its state at HEAD *10
```

*6 ファイル`baz.py`は追跡されたファイルになりました
*7 ファイル`baz.py`は既に追跡されたファイルです
*8 ファイル`baz.py`が非追跡ファイルになりました
*9 チェックアウトによって上書きされるコミットされていない`foo.py`の変更があります。続行しますか？(y/N)
*10 ファイル`foo.py`がHEADの状態に正常にチェックアウトされました

ファイルへの変更を保存するには、<a name="gl_commit"></a>`gl commit`コマンドを使用します。
デフォルトでは、変更されたすべての変更ファイルはコミットされますが、`o/only`、`e/exclude`、および`i/include`フラグを使用して、コミットするファイルのセットをカスタマイズできます。

```bash:コミット
$ gl commit -m "foo and bar"
$ gl commit -m "only foo" -o foo.py
$ gl commit -m "only foo and baz" -o foo.py baz.py
$ gl commit -m "only foo" -e bar.py
$ gl commit -m "only foo and baz" -e bar.py -i baz.py
$ gl commit -m "foo, bar and baz" -I baz.py
```

コミットするファイルのセグメントをインタラクティブに選択できる`p/partial`フラグもあります。

<a name="gl_diff"></a>`gl diff`コマンドを使用して、作業バージョンとコミット済みバージョンのファイルの違いを確認することができます。コミットのように、diffのデフォルトのファイルセットはすべての変更されたファイルのセットですが、`o/only`、`e/exclude`、および`i/include`フラグでカスタマイズできます。

ファイルの削除は、OSの機能(Unixのrmコマンドを使用するなど)を利用して簡単に行えます。Gitlessでは、ファイルが追跡されている場合に削除を検出し、ステータスで削除されたものとしてに表示します。
Gitlessは現在、名前の変更を検出しません。ファイルの名前を変更した場合、Gitlessでは古い名前のファイルが削除され、新しい名前と内容の新しいファイルが作成されたとして解釈します。名前を変更したファイルを再び追跡するには、`gl track`をする必要があります。

<a name="branch"></a>
##ブランチ
ブランチは独立した開発ラインです。 あなたは常になにかしらのブランチで作業しています。各ブランチには独自の履歴があります（<a name="gl_history"></a>`gl history`コマンドで見ることができます）。ブランチを切り替えると、既存のファイルやブランチ上に作成した新しいファイルに対する変更は、他のブランチには反映されません。

新しいブランチを作成するには、<a name="gl_branch"></a>`gl branch`コマンドを使用します。 別のブランチに切り替えるには、<a name="gl_switch"></a>`gl switch`コマンドを使用します。

```bash:ブランチの作成と切り替え
$ gl branch -c develop
✔ Created new branch develop *11
$ gl switch develop
✔ Switched to branch develop *12
```
*11 新しいブランチ"develop"を作成しました
*12 ブランチ"develop"に切り替えしました

すべてのブランチ一覧の表示するには：

```bash:ブランチ一覧の表示
$ gl branch
List of branches:
  ➜ do gl branch <b> to create branch b
  ➜ do gl branch -d <b> to delete branch b
  ➜ do gl switch <b> to switch to branch b
  ➜ * = current branch *13

    * master
      develop
```

*13 ＊は現在のブランチ

各ブランチにはHEADがあり、そのブランチで最後にコミットされたものです。デフォルトでは、新しいブランチの先頭は現在のブランチのHEADと等しくなります。別のコミットを新しいブランチの頭にしたい場合は、`dp/divergent-point`フラグを与えることで可能です。

コミットを指定するには、そのIDを使用するか、"~"を使用して祖先参照で指定できます。`HEAD~n`は、HEADより前のn番目のコミットを参照します。

現在のブランチのHEADを変更するには、`sh/set-head`フラグを使用します。shフラグは、例えば最後のコミットを修正するのに便利です。そうするには、`gl branch -sh HEAD~1`を実行します。現在のブランチのHEADを変更しても作業ディレクトリには反映されませんが、さらに作業ディレクトリを新しいHEADと一致するようにリセットしたい場合は、`gl checkout`を使用します。

最終的にブランチは最終的に変化していくでしょう。あるブランチから現在のブランチに変更を加えるには、*merge*と*fuse*の2つの方法があります。

###merge
あるブランチの変更を現在のブランチにマージするには、<a name="gl_merge"></a>`gl merge develop`を行います。これにより、現在のブランチの変更に加えて、developブランチの変更を含む新しいマージコミットが作成されます。
[f:id:korosuke613:20200925232757p:plain]


###fuse
ブランチをヒューズすると、マージよりもコントロールがしやすくなります。
あるブランチからの変更を現在のブランチにヒューズするときに、ヒューズするコミットと挿入場所を指定することができます。デフォルトでは、すべてのブランチがヒューズされると、挿入場所は分岐点（あるブランチが現在のブランチから分岐したポイント）になります。

たとえば、次の図は、2つのブランチが存在する状況を示しています。master（現在のブランチ）とdevelopです。2つのブランチが共通している最後のコミットは"A"です。このコミットは「分岐点」（masterとdevelopの分岐点であるため）です。<a name="gl_fuse"></a>`gl fuse development`を実行した後、開発中のコミットは、masterの分岐点の後ろに挿入されます。

[f:id:korosuke613:20200925232845p:plain]

他の挿入場所を選択するには、`ip/insertion-point`フラグを使用します。コミットID、HEADまたは`dp/divergent-point`を与えることができます。

[f:id:korosuke613:20200925233129p:plain]

`o/only`または`e/exclude`フラグを使用して、ヒューズされるコミットのセットをカスタマイズできます。

[f:id:korosuke613:20200925232941p:plain]

このプロセス中にコンフリクトが発生する可能性があります。その場合、`gl status`コマンドはそれに応じて変更され、競合しているファイルを示します。競合したファイルを編集すると、<a name="gl_resolve"></a>`gl resolve`(ファイル名を与えるとそれをマークする)で解決されたものとしてマークされます。すべての競合が解消されたら、"gl commit"を実行してヒューズまたはマージを続けます。

ブランチは「アップストリームブランチ」を持つことができます。ブランチにアップストリームが関連付けられている場合、`gl fuse`または`gl merge`を`gl {fuse、merge} upstream_branch`の略記として使用できます。現在のブランチのアップストリームブランチを設定するには、`gl branch -su <upstream_branch>`を使用します。

<a name="tag"></a>
##タグ付け
コミットが何らかの形で特別であることを示すためにタグを使用します。たとえば、<a name="gl_tag"></a>`gl tag`を使用して「v1.0」という名前のタグを作成し、リリースv1.0を表すコミットを指すようにすることができます。

```bash:タグ付け
$ gl tag -c v1.0
✔ Created new tag v1.0 *14
```

*14 新しいタグv1.0を作成した

この場合タグは現在のブランチの先頭を指しますが、`ci/commit`フラグを付けて他のコミットにタグ付けすることもできます。

```bash:タグ一覧の表示
$ gl tag
List of tags:
  ➜ do gl tag <t> to create tag t *15
  ➜ do gl tag -d <t> to delete tag t *16

    v1.0 ➜  tags 311bf7c Ready to release
```

*15 `gl tag <t>`でタグtを作成します
*16 `gl tag -d <t>`でタグtを削除します

<a name="remote"></a>
###リモートリポジトリの操作###
リモートリポジトリを参照するのに、そのURLを常に使用することができますが、より簡単な方法は<a name="gl_remote"></a>`gl remote`コマンドでリポジトリを "リモート"として追加することです。

```bash:リモートへの追加
$ gl remote -c experiment https://github.com/spderosso/experiment
✔ Remote experiment mapping to https://github.com/spderosso/experiment created *17
successfully
  ➜ to list existing remotes do gl remote *18
  ➜ to remove experiment do gl remote -d experiment *19
```

*17 リモートリポジトリ`https://github.com/spderosso/experiment`へのリモート"experiment"を作成しました
*18 `gl remote`で既存のリモート一覧を表示します
*19 `gl remote -d experiment`でリモート"experiment"を削除します

これで、 "experiment"を使ってこのリモートリポジトリを参照し、 `experiment/some-branch`を使用して、 "experiment"に存在する"some-branch"のブランチを参照することができます。

###変更をダウンロードする
リモートブランチからの変更をヒューズまたはマージすることも可能です。たとえば、`gl merge experiment/master`を実行すると、ローカルブランチに存在しないリモートブランチの変更がマージされます。また、`gl fuse`を使用することもできます。

###変更のアップロードする
アップストリームで変更を送信するには、<a name="gl_publish"></a>`gl publish`を使用します。パブリッシュコマンドは、入力を与えられていない場合、デフォルトで現在のブランチのアップストリームブランチを更新します。

###作成、削除、または一覧表示
リモートブランチを作成、削除、または一覧表示するには、ローカルブランチに使用するのと同じ`gl branch`コマンドを使用します。
"gl branch -c experiment/develop"を実行すると、リモート"experiment"に存在する"develop"ブランチが作成されます。デフォルトでは、この新しいブランチのHEADは現在のブランチのHEADと等しくなるので、リモートに存在しないすべてのコミットがアップロードされることに注意してください。
リモートブランチを表示するには、 `gl branch`の `r/remote`フラグを使用します。

###タグの作成、削除、または一覧表示
リモートタグを作成、削除、または一覧表示するには、`gl tag`コマンドを使用します。
`gl tag -c experiment/v1.0`を実行すると、リモートの"experiment"に新しいタグv1.0が作成されます。
`gl tag`の`r/remote`フラグを使ってリモートのタグ一覧を表示することもできます。

リモートからローカルリポジトリを作成すると（gl initコマンドの入力としてURLを渡すことによって）、リモートブランチごとにローカルブランチが作成され、各ローカルブランチは、リモートをアップストリームとして自動的に設定されます。

<a name="gitless_vs_git"></a>
#Gitless vs Git
##変更を保存する
Gitlessにはステージングエリアはありません。
これは、柔軟なコミットコマンドと組み合わせることで、リポジトリへの変更を非常に簡単に保存できます。

[f:id:korosuke613:20200925233227p:plain]


また、ファイルの分類を変更して、追跡したり、非追跡したり、無視したりすることもできます。ファイルがHEADに存在するかどうかは関係ありません。

[f:id:korosuke613:20200925233315p:plain]

##ブランチ
理解しなければならない主な点は、Gitlessではブランチが完全に独立した開発ラインであることです。各ブランチは、ファイルの作業バージョンを互いに分離して保持します。別のブランチに切り替えると、作業ディレクトリの内容が保存され、切り替え先のブランチに対応する内容が取得されます。ファイルの分類も保存されます（ファイルは一部のブランチでは非追跡ですが、別のブランチでは追跡します、Gitlessはこれを覚えています）。

[f:id:korosuke613:20200925233403p:plain]

つまり、Gitlessでは、コミットされていない変更が切り替え先ブランチの変更と矛盾することを心配する必要はありません。

[f:id:korosuke613:20200925233435p:plain]

あなたがヒューズまたはマージの途中にいて、コンフリクトの解決を脇に置いておきたい場合は、そうすることができます。あなたがそのブランチに戻ったときにコンフリクトはそこにあります。

[f:id:korosuke613:20200925233506p:plain]

##リモートリポジトリの操作
Gitlessの他のリポジトリとの同期は、Gitと非常によく似ています。

[f:id:korosuke613:20200925233537p:plain]

<a name="Research"></a>
#Reserch
Gitlessに関連する論文のリスト

* [Purposes, Concepts, Misfits, and a Redesign of Git](http://people.csail.mit.edu/sperezde/oopsla16.pdf)<br>
S. Perez De Rosso and D. Jackson. In Proceedings of the 2016 ACM SIGPLAN International Conference on Object-Oriented Programming, Systems, Languages, and Applications (OOPSLA 2016)
* [What's Wrong with Git? A Conceptual Design Analysis](http://people.csail.mit.edu/sperezde/onward13.pdf)<br>
S. Perez De Rosso and D. Jackson. In Proceedings of the 2013 ACM International Symposium on New Ideas, New Paradigms, and Reflections on Programming & Software (Onward! 2013)



<!-- 記事終わり線 -->
---

<!-- ここに脚注が来る -->
