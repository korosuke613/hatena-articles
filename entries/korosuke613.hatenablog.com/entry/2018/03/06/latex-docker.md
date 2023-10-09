---
Title: DockerでらくらくLaTeX環境構築
Category:
- Docker
- LaTeX
- アーカイブ
Date: 2018-03-06T00:00:00+09:00
URL: https://korosuke613.hatenablog.com/entry/2018/03/06/latex-docker
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613632798458
---

<!-- ここに導入を書く -->
**この記事は、[https://qiita.com/Shitimi_613/items/9706d57fb7bc17cbed0e:title]のアーカイブです。**

卒論を書くために、MacでLaTeX環境の構築を行っていましたが、フォント周りの問題でうまくコンパイルできなかったため、Dockerを用いてLaTeX環境の構築をしようと考えました。

そのため、upLaTeXが使えるDockerイメージを作ろうと思ったのですが、**既に作っている方がいました。**

[日本語が扱える alpine の LaTeX イメージを作った話](http://3846masa.hatenablog.jp/entry/2017/02/08/215920)

本投稿では、上記のサイトを参考にLaTeXの環境構築をしていきます。
**というか、本当に簡単にできます。**

<!--  カスタムURLは`YYYY/MM/DD/name`の形式にする -->
<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]

---

# 追記：2020/01/11
VS CodeのRemote Container機能を使って、**LaTeX執筆環境**を簡単に構築する記事を書きました。

[https://korosuke613.hatenablog.com/entry/2019/06/24/171246:embed:cite]



# paperist/alpine-texlive-ja

使用するDockerイメージ([paperist/alpine-texlive-ja](https://hub.docker.com/r/paperist/alpine-texlive-ja/))の特徴を以下に示します。

* `platex`、`uplatex`が使える（つまり日本語が扱える）
* イメージのサイズがとても軽量（2018年3月6日時点で`letest`が訳400MBのみ）
* デフォルトのフォントがIPAexフォント

# 使い方
**先にDockerをインストールしておいてください。**

今回は試しに用意した以下のTeXファイルをPDFにコンパイルします。

report.tex
```tex
\documentclass[uplatex]{jsarticle}
\和暦
\usepackage[top=25truemm,bottom=25truemm,left=25truemm,right=25truemm]{geometry}
\usepackage[dvipdfmx]{graphicx}
\usepackage{color}

\begin{document}

\title{\huge 報告書}
\author{焼肉 太郎}
\date{\today}
\maketitle

\section{仮面ライダービルドに出てくる仮面ライダー}
	\begin{itemize}
		\item 仮面ライダービルド
		\item 仮面ライダークローズ
		\item 仮面ライダーグリス
		\item 仮面ライダーローグ
	\end{itemize}

\section{仮面ライダービルドに出てくる仮面ライダーもどき}
	\begin{itemize}
		\item ナイトローグ
		\item ブラッドスターク
		\item エンジンブロス
		\item リモコンブロス
	\end{itemize}

\end{document}
```

以下の順序でPDFを生成します。
1. `uplatex`にて`tex`→`dvi`に変換
2. `dvipdfmx`にて`dvi`→`pdf`に変換


## Windows(PowerShell)の場合
`tex`ファイルがあるフォルダに移動して、以下のコマンドを実行します。(今回は`C:Users\hoge\latex\report.tex`をコンパイルします。)
移動しないで実行する場合は`${PWD}`を指定ファイルのあるフォルダの絶対パスに書き換えてください。

```powershell:C：Users\hoge\latex
> docker run --rm -v ${PWD}:/workdir paperist/alpine-texlive-ja uplatex report.tex
> docker run --rm -v ${PWD}:/workdir paperist/alpine-texlive-ja dvipdfmx report.dvi
```

## Mac(bash)の場合
`tex`ファイルがあるフォルダに移動して、以下のコマンドを実行します。(今回は`/Users/hoge/latex/report.tex`をコンパイルします。)
移動しないで実行する場合は`$PWD`を指定ファイルのあるディレクトリの絶対パスに書き換えてください。


```bash:/Users/hoge/latex
$ docker run --rm -v $PWD:/workdir paperist/alpine-texlive-ja uplatex report.tex
$ docker run --rm -v $PWD:/workdir paperist/alpine-texlive-ja dvipdfmx report.dvi
```

# 結果
以下のPDFが生成されるはずです。

[f:id:korosuke613:20200926195608p:plain]

# おまけ - TeXstudio上で実行する
**わざわざコマンドなんて打ってられっか！**という方のために、私が愛用しているTeXのIDEである**TeXstudio**でこの方法を使う方法を記します。

1. TeXstudioを開き、「TeXstudioの設定」を開きます。(Windowsの場合: `オプション(O)` → `TeXstudioの設定(C)`), (Macの場合: `TeXstudio` → `環境設定`)

2. 「ビルド」タブの「既定のコンパイラ」を「LaTeX」に設定します。
3. 「ビルド」タブの「ビルド & 表示」を「DVI->PDFチェーン」に設定します。
4. 「コマンド」タブの「LaTeX」に`docker run --rm -v C:Users\hoge\latex:/workdir paperist/alpine-texlive-ja uplatex -interaction=nonstopmode %.tex`を設定します。
5. 「コマンド：」タブの「DviPdf」に`docker run --rm -v C:Users\hoge\latex:/workdir paperist/alpine-texlive-ja dvipdfmx %.dvi`を設定します。

<details>
<summary>**ビルドタブ設定例**</summary>
![ビルドタブ](https://qiita-image-store.s3.amazonaws.com/0/125406/738f61f2-b54d-efd5-6801-1498557a260a.png)
</details>

<details>
<summary>**コマンドタブ設定例**</summary>
![コマンドタブ](https://qiita-image-store.s3.amazonaws.com/0/125406/3c368a96-4dc0-3c6e-80b9-0ac94899ced4.png)
</details>


上記の設定を行ったら、「ビルド & 表示」ボタンを押してみましょう。
PDFが生成され、以下のような画面になるはずです。

[f:id:korosuke613:20200926195628p:plain]

しかし、この方法では、上記で示した`${PWD}`や`$PWD`が使えません...
`tex`ファイルの存在するディレクトリの**絶対パス**を、毎回設定しなおさなければならないという欠点があります...


# おわりに
`paperist/alpine-texlive-ja`イメージを使うことで、**簡単に日本語を扱うLaTeX環境の構築ができます。**

Docker全般に言えることですが、マシンの環境を汚すこともなく、インストールとアンインストールがらくらくにできるため、非常におすすめです。フォントもOSに依存しません。
もっと便利な使い方、コマンドを知っている方は、ぜひコメントしてください。

また、他人が作成したDockerイメージを利用する際は、悪意のないイメージであることを確認してから利用しましょう。今回の`paperist/alpine-texlive-ja`は、Docker Hubで[Dockerfile](https://hub.docker.com/r/paperist/alpine-texlive-ja/~/dockerfile/)にてビルドされているため、このDockerfileを見ることで安心できます。

作者様に感謝です。


<!-- 記事終わり線 -->
---

<!-- ここに脚注が来る -->
