---
Title: 【Python3.6~】f-stringsを使おう
Category:
- Python
- プログラミング
- アーカイブ
Date: 2018-06-07T00:00:00+09:00
URL: https://korosuke613.hatenablog.com/entry/2018/06/07/f-string
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613632486852
---

<!-- ここに導入を書く -->
<b>この記事は、[https://midorigame201845.hateblo.jp/entry/2018/06/07/171916:title]のアーカイブです。</b>

Python3.6より、<code>str</code> に変数を埋め込む方法として、f-stringsが追加されました。
日本語で言うと、 <a href="https://docs.python.jp/3/reference/lexical_analysis.html#formatted-string-literals">フォーマット済み文字列リテラル</a>となります。
Python3.6未満のバージョンでは、<code>str.format()</code>や、<code>%</code>記号を用いることで同じような振舞いを実現できていました。今回は、それらとf-stringsの違いと、使い方を書いていこうと思います。

<!--  カスタムURLは`YYYY/MM/DD/name`の形式にする -->
<!-- 続きを読むのやつ -->
<!-- more -->

<hr />

目次

[:contents]

<hr />


* 書き方
f-stringsは以下のように記述します。

>|python|
>>> a = 1
>>> b = 2
>>> print(f'{a} + {b} = {a + b}')
1 + 2 = 3
||<

使い方は簡単で、<code>str</code>のリテラルの前に<code>f</code>をくっつけること（<code>f''</code>）で、ただの<code>str</code>がformatted stringsになります。
変数の埋め込みは<code>{変数}</code>で行います。

上の例を見ると、<code>{a}</code>と<code>{b}</code>がそれぞれ<code>1</code>と<code>2</code>に置き換わって出力されていることがわかります。

また、<code>{a + b}</code>が<code>3</code>になっていることにお気づきでしょうか？
<code>{}</code>の中には式文(<a href="https://docs.python.jp/3/reference/simple_stmts.html#expression-statements">expression_stmt</a>)を入れることができるので、<code>a + b</code>という式を評価した値を出力することになります。
もちろん、関数を呼び出すことも可能なので、以下のようなこともできます。

>|python|
>>> print(f'{a} + {b} = {print(a + b)}')
3
1 + 2 = None
||<

上の場合、<code>print()</code>は2回呼び出されています。
最初の<code>print()</code>を実行する際に、<code>f'{a} + {b} = {print(a + b)}'</code>を評価します。3つの式文の中でも、<code>{print(a + b)}</code>を評価する際に<code>print(a + b)</code>を実行するため、先に、<code>3</code>を出力します。そして、<code>print()</code>は<code>None</code>を返す為、1+2の答えは<code>None</code>になるわけです。

** 書式指定
f-stringsでも、<code>str.format()</code>と同様に、書式指定ができます。（<s>当たり前</s>）
例を以下に示します。

>|python|
>>> a = 0.1
>>> b = 0.02
>>> c = 0.003
>>> print(f"{a} + {b} + {c} = {a + b + c}")
0.1 + 0.02 + 0.003 = 0.12300000000000001
>>> print(f"{a} + {b} + {c} = {a + b + c :.2}")
0.1 + 0.02 + 0.003 = 0.12
||<

<code>{}</code>の中の式文の後に<code>:</code>を付けることで、書式の指定が可能です。
上の例だと、<code>{a + b + c :.2}</code>とすることで、小数点以下2桁を丸めています。

** 改行
f-stringsを定義する上で、1行がとても長くなってしまった場合、以下の方法で、<b>見た目上の改行</b>ができます。(<s>て言ってもこれは<code>str</code>に言えることなのですが</s>)

>|python|
>>> a = 1
>>> b = 2
>>> c = 3
>>> print(f"これは{a}行目"
...       f"これは{b}行目"
...       f"これは{c}行目")
これは1行目これは2行目これは3行目
||<


* 既存の方法との比較
歴史的には、<code>%</code>記法、<code>str.format()</code>、f-stringsの順に登場しています（<s>確か</s>）。

** <code>%</code>記法
この方法は、C言語の<code>printf()</code>に似ています。<code>str</code>の埋め込みたい場所に、<code>%d</code>や<code>%s</code>のフォーマット指定子を埋め込み（<code>'%d + %d = %d'</code>の部分）、その<code>str</code>と、埋め込むオブジェクトを<code>%</code>でつなぐことで実現できます。

>|python|
>>> print('%d + %d = %d' % (1, 2, 3))
1 + 2 = 3
||<


** <code>str.format()</code>
この方法は、<code>str</code>のメソッド<code>format()</code>を呼び出しています。
<code>str</code>の埋め込みたい場所に、<code>{}</code>を埋め込み、<code>.format()</code>の引数に式文を指定することで実現できます。

<b><code>%</code>記法に比べて、型をしていしなくて良いというメリットがあります。</b>

>|python|
>>> print('{} + {} = {}'.format(1, 2, 3))
1 + 2 = 3
||<

** f-strings
f-stringsは上に記述した通りですが、<b><code>str.format()</code>に比べて、直感的に記述できるというメリットがあります。</b>

* おわりに
f-stringsは直感的に<code>str</code>に変数を埋め込むことができます。今まで<code>str.format()</code>を利用していた方も、ぜひ、利用を検討してください。

<!-- 記事終わり線 -->
<hr />

<!-- ここに脚注が来る -->
