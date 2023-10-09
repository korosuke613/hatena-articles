---
Title: LaTeXでWarningをignoreする
Category:
- LaTeX
- ノウハウ
- 生産性向上
Date: 2020-01-17T21:47:52+09:00
URL: https://korosuke613.hatenablog.com/entry/2020/01/17/latexsilence
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613498948464
---

<!-- ここに導入を書く -->
LaTeXを使って修論を書いていたところ、謎のwarningがでました。

```
LaTeX Font Warning: Some font shapes were not available, defaults substituted.

LaTeX Font Warning: Font shape `JT2/mc/b/n' undefined
(Font)              using `JT2/mc/m/n' instead on input line 130.

LaTeX Font Warning: Font shape `JY2/mc/b/n' undefined
(Font)              using `JY2/mc/m/n' instead on input line 130.

〜〜似たようなwarningが続く〜〜
```

[放置してても問題は無い](https://texwiki.texjp.org/?%E3%82%88%E3%81%8F%E3%81%82%E3%82%8B%E8%B3%AA%E5%95%8F#bf583189)ようですが、嫌になったので、warningを見えなくしました((warningそのものを取り除くべきだと考えたのですが、何十行か追加しなければならなかったので止めました。))。

<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]

<!-- ここに広告が入る -->
---

# 3行でわかるignoreする方法

```tex
\usepackage{silence}
\WarningFilter{latexfont}{Some font shapes}
\WarningFilter{latexfont}{Font shape}
```
これで、上記のwarningをignoreできます。

# 簡単な解説

CTANにある、[silence](https://ctan.org/pkg/silence)というパッケージを使うことで、任意のwarningをignoreできます。

`\WarningFilter`コマンドで、ignoreするwarningを指定します。書式は以下の通りです。

```tex
\WarningFilter{パッケージ名}{ignoreしたいメッセージ}
```

今回ignoreしたいメッセージは、`LaTeX Font`によるwarningだったため、第一引数のパッケージ名には`latexfont`を指定します。((LaTeX Fontがlatexfontだとわかった理由は、[こちらのサイト](https://www.semipol.de/2018/06/12/latex-best-practices.html#filtering-warnings)で発見したためです。パッケージ名をlatexに指定しても、ignoreしてくれませんでした。場合によってはパッケージ名を探すのが大変かも知れません。))


まずは、`Some font shapes were not available, defaults substituted.`をignoreするために、`\WarningFilter{latexfont}{Some font shapes}`を宣言しました。第二引数はignoreしたいwarningメッセージに前方一致する文字列である必要があるようです。したがって、第二引数は`Some font shapes`としています。

`Font shape`から始まるwarningも同様に`\WarningFilter{latexfont}{Font shape}`を宣言することでignoreしています。

# もっと知りたい人
他にも、エラーメッセージをignoreするコマンド(`\ErrorFilter`)や、メッセージをignoreするタイミングを調整するコマンド(`\ActivateWarningFilters`、`\DeactivateWarningFilters`)といったコマンドが用意されています。

もっと知りたい人は、[公式ドキュメント](http://ftp.yz.yamagata-u.ac.jp/pub/CTAN/macros/latex/contrib/silence/silence-doc.pdf)を参照してください。

<!-- 記事終わり線 -->
---

<!-- ここに脚注が来る -->

