---
Title: LaTeXでwarningを出してくれるTODOコマンドを2行で作った
Category:
- LaTeX
- ノウハウ
- 生産性向上
Date: 2020-01-17T22:27:23+09:00
URL: https://korosuke613.hatenablog.com/entry/2020/01/17/latextodo
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613499000733
---

<!-- ここに導入を書く -->
卒論、修論シーズンですね。
僕も現在修論をゴリゴリ書いています。

後で記述したいことや、後で直したいことをTODOみたいにとりあえず書いておきたいことってありませんか？

コメントを使うことでTODOを入れることは可能ですが、もしかしたら書いたまま忘れてしまうかもしれません。

今回は、**warningを出力してくれるTODOコマンドを作りました。**

<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]

<!-- ここに広告が入る -->
---

# 4行でわかる使い方

```tex
\newcommand\todo[1]{\PackageWarning{Todo}{Detection TODO:#1}\textcolor{red}{(TODO:#1)}}

% TODOを入れたい場所に記述する。
\todo{VDMJに関することを書く。既存のBWDM、拡張したBWDMの章で説明したこともこっちで書くべきかもしれない。}
```

例えば、上のようにすると、下の画像のように、
<span style="color: #ff0000">`(TODO:<メッセージ>)`</span>という書式でPDFに<span style="color: #ff0000">赤字</span>のメッセージを出力してくれます。
赤字なのでわかりやすいですね。

<figure class="figure-image figure-image-fotolife" title="TODOコマンドを使ったらこうなる">[f:id:korosuke613:20200117215804p:plain]</figure>

しかも、コンパイル時に下のようなwarningを出力してくれます。

```
Package Todo Warning: Detection TODO:ぜんぜんBWDMの説明になっていないので、もっと既存のBWDMにできることを説明する。 on input line 341.
```

warningを出力してくれるので、例えばVS CodeとLaTeX-Workshopを使っていた場合、以下の画像のように、**下線（ニョロニョロ）を表示してくれます。**

<figure class="figure-image figure-image-fotolife" title="VS Codeを使ってたら、下線も引いてくれる。">[f:id:korosuke613:20200117220520p:plain]</figure>

# todoコマンドの定義
`\todo`コマンドの定義について説明します。

```tex
\newcommand\todo[1]{\PackageWarning{Todo}{Detection TODO:#1}\textcolor{red}{(TODO:#1)}}
```

`\newcommand\コマンド名[引数の数]{行う処理}`で新しいコマンドを定義することができます。
今回は、`\newcommand\todo[1]`としているので、「**`\todo`という引数が1つのコマンドを定義する**」という意味になります。

「行う処理」について説明します。

`\PackageWarning{パッケージ名}{出力するwarningメッセージ}`で、warningを出すことができます。パッケージ名に関しては、適当な名前で構いません。(~~たぶん~~)出力するwarningメッセージは、`Detection TODO:#1`としています。`#1`には定義するコマンドの第一引数が代入されます。したがって、`\todo{あああ}`という記述をすると、`Detection TODO:あああ`というwarningが出力されます。

そのあとは、warningを出す処理の後に、`\textcolor{red}{(TODO:#1)}`を定義しています。これで、出力されるPDFに実際に<span style="color: #ff0000">TODO:あああ</span>といった文字列が記述されます。

使いやすいようにカスタマイズしてみてください。

# ちなみに
ちなみに、VS CodeでLaTeXを記述するのにおすすめの方法が、Remote Container機能を使うことです。以下のリンクの方法でできます。（~~僕が書いた記事です。~~）

[https://korosuke613.hatenablog.com/entry/2019/06/24/171246:embed:cite]



<!-- 記事終わり線 -->
---

<!-- ここに脚注が来る -->
