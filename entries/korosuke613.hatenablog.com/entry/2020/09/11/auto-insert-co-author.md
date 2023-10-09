---
Title: "モブプロで役立つ？？git rebaseでsquashする時にそれまでの著者をCo-authored-byに自動で追加するgit hookを作りました\U0001F389"
Category:
- プログラミング
- ノウハウ
- 生産性向上
- 作ってみた
Date: 2020-09-11T00:42:43+09:00
URL: https://korosuke613.hatenablog.com/entry/2020/09/11/auto-insert-co-author
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613624051120
---

<!-- ここに導入を書く -->

こんにちは。生産性向上チームで日々生産性を上げている平木場です((社内ではきばちゃんと呼ばれてます))。

いきなりですが、みなさんモブプロしてますか？

モブプロをしていると複数人が作ったcommitをsquashでまとめる時があるのではないでしょうか？しかし、squashをすると、**それまでcommitした人の貢献がなかったこと**になってしまいます。

そこで**git rebaseでsquashする時に、それまでの著者をCo-authored-by((commitメッセージに追記することで共著者として登録できる。 https://docs.github.com/en/github/committing-changes-to-your-project/creating-a-commit-with-multiple-authors#creating-co-authored-commits-on-the-command-line))に自動で追加するgit hookを作りました。**

<figure class="figure-image figure-image-fotolife" title="">![](https://raw.githubusercontent.com/korosuke613/auto-insert-co-author-githook/master/demo.gif)
<figcaption>auto-insert-co-author-githookが動作している様子</figcaption></figure>

<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]


<!-- ここに広告が入る -->
---

# 背景

## git rebaseでのsquashの弊害
モブプロではドライバーを交代するので、なんらかの方法でソースコードを共有する必要があります。

例えば僕が所属しているチームでは、ドライバーがソースコードをZoomの画面共有で視覚的に共有し、交代時にそれまでの作業をWIPとしてcommit、pushしてソースコードの実体も共有しています((僕たちはもっぱら「WIPを積む」と表現します))。

この方法だとゴミのようなcommitが履歴に残ってしまうため、mainブランチにマージする段階でcommitをまとめる必要があります。その時に`git rebase -i main`でcommitをsquashします。

しかし、**squashするとそれまでのcommitのauthorが文字通り押し潰されます。**

何が問題なのか、例を示して説明します。
例えば、以下のような履歴があります。（上が最新）

|hash|message|author|commiter|branch|
|---|---|---|---|---|
|`34fa9e`|wip|hoge|hoge|HEAD|
|`ab1fe3`|wip|foo|foo||
|`5eba55`|WIP|fuga|fuga||
|`f12acf`|Merge pull request...|foo|github|main|


この状態で、ユーザー*hoge*が`git rebase -i main`して、全てのwipを`5eba55`にまとめると下のようになります((ついでにcommitメッセージを意味のあるものにしてます))。

|hash|message|author|commiter|branch|
|---|---|---|---|---|
|`5eba55`|feat: add feature|fuga|hoge|HEAD|
|`f12acf`|Merge pull request...|foo|github|main|

**`5eba55`のauthorであるユーザ*fuga*はrebase後もそのままauthorになり、rebaseをした ユーザ*hoge*がcommitterとなりました。**

この状態でgithubにpushすると、**このcommitはfugaとhogeが作ったことになり、同じく貢献したはずのfooは履歴に残らず貢献していないことになってしまいます。**このcommitがmainにマージされてもユーザ*foo*に草は生えません。

エンジニアによってはやる気低下の原因になってしまいます...

## Co-authored-byでauthorを複数人設定できる
ではどうしようもないのかというと、そうではありません。

GitHubには、[commitメッセージを工夫することで共著者を設定できる機能](https://docs.github.com/en/github/committing-changes-to-your-project/creating-a-commit-with-multiple-authors#creating-co-authored-commits-on-the-command-line)があります。

下のように、commitメッセージの最後に空行を2つと`Co-authord-by: 作者の名前 <メールアドレス>`を追加します。
<script src="https://gist.github.com/korosuke613/93dfeb2b6b08338f2e39862ae5f5a390.js?file=Co-authored-by"></script>


このcommitをGitHub上で見るとこうなります。

<figure class="figure-image figure-image-fotolife" title="GitHubのcommitに複数のユーザアイコンが並んでいる。クリックしたらその人のアカウントに飛ぶ">[f:id:korosuke613:20200910203254p:plain:title=]
<figcaption>GitHubのcommitに複数のユーザアイコンが並んでいる。クリックしたらその人のアカウントに飛ぶ</figcaption></figure>

commitしたユーザとCo-authored-byで追加したユーザのアイコンが並んでいますね。(わかりづらいですがアイコンが3つ並んでいます。)

このように**Co-authored-byを使うことでコミットしたユーザ以外も貢献したことにできます**。
しかし、毎回毎回まとめるcommitのcommitterをcommitメッセージに追加するのは面倒ですね...

# auto-insert-co-author-githook
そこで、**squashするcommit履歴からcommitterを抽出し、まとめるcommitのcommitメッセージにCo-authored-byを自動で追加するgit hookを作成しました**。

[https://github.com/korosuke613/auto-insert-co-author-githook:embed:cite]

下の画像のように、squashするcommitのcommitterが自動でCo-authord-byに追加されます。

**これでみんな仲良く草を生やすことができますね🌲**

<figure class="figure-image figure-image-fotolife" title="commitメッセージの末尾にCo-authored-byが追記されてる">[f:id:korosuke613:20200910204428p:plain:title=]
<figcaption>commitメッセージの末尾にCo-authored-byが追記されてる</figcaption></figure>

## インストール
インストール方法はいくつかあります。詳細は[README.md](https://github.com/korosuke613/auto-insert-co-author-githook/blob/master/README.md)をご覧ください。

- [pre-commit](https://pre-commit.com/)を利用する（**とってもおすすめ🤴**）
- [lefthook](https://github.com/Arkweid/lefthook)を利用する（２番目におすすめ）
- [insert-co-author.sh](https://github.com/korosuke613/auto-insert-co-author-githook/blob/master/insert-co-author.sh)を`prepare-commit-msg`にリネームして`.git/hooks/`に配置する（従来の方法）

特にpre-commitはすばらしく、プロジェクトルートに下記のような`.pre-commit-config.yaml`を配置して`pre-commit install --hook-type prepare-commit-msg`を実行するだけでインストールできます。非常に簡単でおすすめです。

<script src="https://gist.github.com/korosuke613/93dfeb2b6b08338f2e39862ae5f5a390.js?file=.pre-commit-config.yaml"></script>

## 仕組み
auto-insert-co-author-githookの本体は[`insert-co-author.sh`](https://github.com/korosuke613/auto-insert-co-author-githook/blob/master/insert-co-author.sh)です。`insert-co-author.sh`がcommitメッセージを書き換えます。

`insert-co-author.sh`は[`prepare-commit-msg`](https://git-scm.com/docs/githooks#_prepare_commit_msg)で起動することを想定しています。(`prepare-commit-msg`フックはcommitメッセージを編集する手前で呼び出されるhookです。)

### `insert-co-author.sh`が呼び出されるまでの流れ

1. `git rebase -i main`で対話モードに入るとエディタが起動し、各commitのコマンドの編集画面に遷移する。
2. pickをsquashに書きかえ終えると、squashする古いcommitから一つずつ自動でrebaseされる。
3. squashするcommitが全てrebaseされるとcommitメッセージを書き換えるためにエディタが起動するため、エディタの起動前に`insert-co-author.sh`hookが呼び出される。
4. `insert-co-author.sh`が動作する。

### `insert-co-author.sh`がcommitメッセージにCo-authored-byを追加する流れ
最新のコードは[リポジトリ](https://github.com/korosuke613/auto-insert-co-author-githook)を参照してください。

<script src="https://gist.github.com/korosuke613/93dfeb2b6b08338f2e39862ae5f5a390.js?file=insert-co-author.sh"></script>


1. `git status`でgit rebaseの対話モードであるかどうか確認する。違ったら終了する(( prepare-commit-msg hookはgit rebase -i以外のcommitメッセージを編集する時も呼び出されるため。))。
2.  `.git/rebase-merge/done`ファイルにあるsquashするcommitのhashを取得する。
3. `2.`で得たsquashするcommitをgit showで表示する。この時、`Co-authord-by: 作者の名前 <メールアドレス>`の形式になるようにフォーマットする。
4. `3.`の結果を一意にし、`.git/COMMIT_EDITMSG`に追記する。

#### squashするcommitを取得する方法(`.git/rebase-merge/done`)
`.git/rebase-merge/done`はrebase中にgitによって作成される一時ファイルです。

<script src="https://gist.github.com/korosuke613/93dfeb2b6b08338f2e39862ae5f5a390.js?file=cat_.git_rebase-merge_done"></script>


**このファイルにはどのcommitをpickするか、squashするかなどの内容が含まれています**((たぶん厳密には違う))。
このファイルからsquashするcommitを取得します。(上記`2.`)

#### commitメッセージにCo-authored-byを追加する方法(`.git/COMMIT_EDITMSG`)
`.git/COMMIT_EDITMSG`はcommitメッセージの編集でエディタが起動する前にgitによって作成される一時ファイルです。

エディタが起動すると、すでに何らかの文字列が存在していると思います。
その理由は、私たちは**commitメッセージを編集時に、デフォルトの文字列が入った`.git/COMMIT_EDITMSG`を直接エディタで開いている**ためです。

<figure class="figure-image figure-image-fotolife" title="commitメッセージの編集画面(vim)のステータスバーにCOMMIT_EDITMSGと表示されてる">[f:id:korosuke613:20200910214034p:plain:title=]<figcaption>commitメッセージの編集画面(vim)のステータスバーにCOMMIT_EDITMSGと表示されてる</figcaption></figure>

編集後にエディタを閉じるとgitは`.git/COMMIT_EDITMSG`を解析してcommitメッセージとしているわけですね。

エディタの起動直前に`.git/COMMIT_EDITMSG`にCo-authord-byを追記するため(上記`4.`)、commitメッセージにCo-authord-byを追加することができます。

## 課題
ただ、本記事の方法では、git hookの性質上、**チームメンバごとかつリポジトリごとにauto-insert-co-author-githookを設定しなければならない**という課題があります。

チームメンバ全員が新しいリポジトリで作業を始めるたびにインストールの手続きをしなければならないのはそれはそれで面倒だと思います。

一応、[globalなgit-hookを設定する方法](https://qiita.com/ik-fib/items/55edad2e5f5f06b3ddd1)もありますが、これはこれで面倒です。pre-commitの設定ファイルをグローバルにできれば少しは楽になるのですが...

また、**bashで記述しているので、windowsではおそらく動作しません**。

~~ついでに、テストが十分にできていないのでバグが結構ありそうです。~~

# 作ってみて
たった数十行のシェルスクリプトですが、**作るのが非常に辛かった**です。

goあたりで書きたかったのですが、OSごとに実行ファイルを作る必要があり面倒だと感じたのでシェルスクリプトで記述しました。結局Windowsでは動作しないのであんまり変わらなかったのですが、気づくのが遅かったです。いつかもっと高水準な言語で書きたいですね。

git rebaseの仕組みもいまいちよくわかってなかった状態で手探りで作り始めたので、「できた
〜」と「できたと思ったけど実はできてなかったわ」を何回も繰り返しました。**おかげで作る前よりほんのちょっとgitと仲良くなれた**((~~git完全に理解した~~))気がします。

~~正直まだまだよくわかってません。この記事でのgit用語の使い方や説明もあんまり自信ありません。~~

[https://twitter.com/Shitimi_613/status/1301884957728542721?s=20:embed]

また、このhookをTwitterでつぶやいたところ、めずらしく**8リツイート23いいね**とプチバズりしました((僕にとってはなかなか多い数字なのです。))。弊社のつよつよエンジニアの方たちもリツイートしてくれたのも大きかったと思います。

正直git rebaseでsquashして僕のように困った人がどれだけいるのかさっぱりだったんですけど、**まあまあ同じ悩みを持ってる人がいるっぽいことがわかってよかった**です。

**ぜひ使ってみてフィードバックください！**

<!-- 記事終わり線 -->
---

<!-- ここに脚注が来る -->
