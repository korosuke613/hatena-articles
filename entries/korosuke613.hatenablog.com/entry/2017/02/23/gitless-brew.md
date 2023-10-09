---
Title: brewでGitlessをインストールする方法とglコマンドが使えない問題の解決策
Category:
- アーカイブ
- プログラミング
Date: 2017-02-23T00:00:00+09:00
URL: https://korosuke613.hatenablog.com/entry/2017/02/23/gitless-brew
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613632478863
---

<!-- ここに導入を書く -->
**この記事は、[https://qiita.com/Shitimi_613/items/28e0730a7858fd79d4fd:title]のアーカイブです。**

Gitlessはgitの仕組みを簡単にするために作られたCUIツールです。

詳しくは[Gitless公式サイト(英語)](http://gitless.com/)または、[gitをシンプルでわかりやすくするツールGitlessの紹介[和訳]](http://qiita.com/Shitimi_613/items/8b25de2c586cc85737af)をご覧ください。

Gitlessのインストール方法はいくつかあるのですが、今回はHomebrewを利用してインストールします。
また、zshを使っている人はglコマンドが正しく実行できない可能性があるので、その場合は[こちら](#zsh)をご覧ください。

<!--  カスタムURLは`YYYY/MM/DD/name`の形式にする -->
<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]

---

#環境
* macOS Sierra 10.12.3
* Homebrew 1.1.10
* zsh 5.2 (x86_64-apple-darwin15.2.0)

#インストール方法
1. Homebrewを更新して、formulaを最新版にする((Error: The /usr/local directory is not writable.と出て、アップデートできない場合、[brew update: /usr/local 権限周りの問題＝更新で解消](http://qiita.com/masa_36/items/a811c9335e2e985f6adc)を参考にしてください。))。  
`$ brew update`
2. Gitlessをインストールする。  
`$ brew install gitless`
3. インストール完了！

<a name="zsh"></a>
#gl initができない！
##問題
さっそくGitlessの練習用のフォルダを作り、`gl init`でリポジトリを作成しようとしたところ、次のエラーが発生しました。

```bash:zsh
$ gl init
fatal: Not a git repository (or any of the parent directories): .git
```

今からリポジトリを作るのに、"リポジトリ(.git)がない！"と怒られました。

##原因
しょうがないのでhelpを見ようと思い`gl -h`を実行したところ、次のメッセージがでました。

```bash:zsh
$ gl -h
usage: git pull [<options>] [<repository> [<refspec>...]]
.
.
.
```

どうやら`gl`は`git pull`を略したaliasとなっていたみたいですが、私は身に覚えがありません。
調べたところ、[zshが標準で設定していた](https://github.com/robbyrussell/oh-my-zsh/blob/master/plugins/git/git.plugin.zsh)みたいです。

> 170 alias gl='git pull'
>
>*[git.plugin.zsh](https://github.com/robbyrussell/oh-my-zsh/blob/master/plugins/git/git.plugin.zsh)より引用*

##解決策
.zshrcにaliasを追加しましょう！

```bash:.zshrc
alias gl='gl'
```

もう一度`gl init`を実行してみます。

```bash:zsh
$ gl init
✔ Local repo created in /Users/hoge/foo
```

こんどこそインストール完了！


<!-- 記事終わり線 -->
---

<!-- ここに脚注が来る -->
