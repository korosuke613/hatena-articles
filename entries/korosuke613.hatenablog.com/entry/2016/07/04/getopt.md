---
Title: CでプログラムにコマンドラインからUNIX風のオプションを渡す方法(getopt関数)
Category:
- C/C++
- アーカイブ
- プログラミング
Date: 2016-07-04T00:00:00+09:00
URL: https://korosuke613.hatenablog.com/entry/2016/07/04/getopt
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613632471605
---

<!-- ここに導入を書く -->
**この記事は、[https://qiita.com/Shitimi_613/items/1b0eb36ca6413a521ec2:title]のアーカイブです。**

自分で作ったCのプログラムにUNIXライクなオプションを与えて動作を変えたいと思ったことはないだろうか？

おそらくまず思いつく方法がコマンドライン引数(main関数に渡す引数のこと)をargcの数だけstrcmpで比較する方法だろう。

もちろんこの方法でも実現することはできるが、もっと簡単に実装できる便利な関数があるので紹介する。

<!--  カスタムURLは`YYYY/MM/DD/name`の形式にする -->
<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]

---



#getopt関数
##概要
```c
int getopt(int argc, char * const argv[], const char *optstring);
extern char *optarg;
extern int optind, opterr, optopt;
```

getopt関数とはコマンドライン引数のオプション(ハイフン'-'で始まる文字)を解析する関数である。
unistd.hをインクルードして使う。
第1、第２引数は、main関数の引数であるargc,argvをそのまま渡し、第３引数には使用するオプション文字の集合を文字列にして渡す。
返り値で返される値には大きく分けて３つのパターンがある。

* optstringで指定したオプション文字が認識された場合、そのオプション文字を返す。
* 指定していないオプション文字が認識された場合、または、引数が必要なオプションに引数を渡さなかった場合、'?'を返す。
* オプション文字以外の引数(ハイフン'-'がついてないコマンドライン引数)が認識された場合、-1を返す。

なお、返り値は全て整数型となる。

##optstringの書き方
getopt()の第３引数には、使用したいオプション文字の集合をつなげて文字列にして渡す。

```c
getopt(argc, argv, "fgh:");
```

例えばf,g,hをオプションに使いたい場合は、上記の様に記述する。
':'をつけることで、その直前の文字を"引数を取るオプション文字"とすることができる。

##４つのグローバル変数について
この関数は、*optarg, optind, opterr, optoptという４つのグローバル変数を利用している。

* **\*optarg** - getopt()が引数付きのオプションを検知した場合、その引数が格納される。もし、引数を取らないオプションを検知した場合は、NULLが格納される。
* **optind** - 初期値は1。getopt()がargv[]を一つ解析するたびに1ずつ増えていく。getopt()はargv[getopt]を解析することでargv[]を順々に解析することができる。
* **opterr** - 初期値は1。opterr = 0 にすると、getopt()が出す標準エラー出力が無効化される。
* **optopt** - 初期値は0。解析したオプション文字が格納される。

##使用例

```c
#include <stdio.h>
#include <unistd.h>

int main(int argc, char* argv[])
{
    int i, opt;

    opterr = 0; //getopt()のエラーメッセージを無効にする。

    while ((opt = getopt(argc, argv, "fgh:")) != -1) {
        //コマンドライン引数のオプションがなくなるまで繰り返す
        switch (opt) {
            case 'f':
                printf("-fがオプションとして渡されました\n");
                break;

            case 'g':
                printf("-gがオプションとして渡されました\n");
                break;

            case 'h':
                printf("-hがオプションとして渡されました\n");
                printf("引数optarg = %s\n", optarg);
                break;

            default: /* '?' */
                //指定していないオプションが渡された場合
                printf("Usage: %s [-f] [-g] [-h argment] arg1 ...\n", argv[0]);
                break;
        }
    }

    //オプション以外の引数を出力する
    for (i = optind; i < argc; i++) {
        printf("arg = %s\n", argv[i]);
    }

    return 0;
}
```

上記のプログラム(sample.c)はオプションとオプション以外のコマンドライン引数を区別して出力するプログラムである。
オプション文字かそれ以外の引数かを判別することができる。
-f,-g,-hがオプション文字となるが、-hは引数を取るオプション文字となっている。

**入力例1：-f -g aaa bbb**

```text:
$ ./sample -f -g aaa bbb
-fがオプションとして渡されました
-gがオプションとして渡されました
arg = aaa
arg = bbb
```
オプション文字かどうか判別できている。

**入力例2：-fg aaa bbb**

```text:
$ ./sample -fg aaa bbb
-fがオプションとして渡されました
-gがオプションとして渡されました
arg = aaa
arg = bbb
```
-fgという風につなげても認識する。

**入力例3：-fh aaa bbb**

```text:
$ ./sample -fh aaa bbb
-fがオプションとして渡されました
-hがオプションとして渡されました
引数optarg = aaa
arg = bbb
```
-hは引数を取るオプションなので、"aaa" が-hに対する引数として処理されている。

**入力例4：-o aaa bbb**

```text:
$ ./sample -o aaa bbb
Usage: ./sample [-f] [-g] [-h argment] arg1 ...
```
指定していないオプションを入力すると、getopt()は'?'を返す。

**入力例5：aaa bbb -f**

```text:
$ ./sample aaa bbb -f
arg = aaa
arg = bbb
arg = -f
```
引数より後にオプションを持ってきてしまった場合、ただの引数として処理されてしまうので注意が必要。

**入力例6：-hgf**

```text:
$ ./sample -hgf
-hがオプションとして渡されました
引数optarg = gf
```
ユーザが誤って引数が必要なオプションに引数を渡さず、オプションを渡してしまった場合、getopt()ではその判断ができないので注意が必要。


###ハイフンで始まる値をオプションでない引数としたい場合
**入力例7：-f -100**

```
./sample -f -100
-fがオプションとして渡されました
Usage: ./getopt_func [-f] [-g] [-h argment] arg1 ...
```
"-100"を入力したくても、オプションと判断されてしまうため、期待と違う結果となってしまう。そういう場合は下のように入力する。

**入力例8：-f -- -100**

```
./sample -f -- -100
-fがオプションとして渡されました
arg = -100
```
"--"をオプションの後に入れることで、getopt()にオプションの入力は終わったと判断させることができ、"-100"といった値も入力できる。

###opterrの値を1にするとどうなるか
sample.cの`opterr = 0;`の部分をコメントアウトする。

```c
#include <stdio.h>
#include <unistd.h>

int main(int argc, char* argv[])
{
    int i, opt;

    //opterr = 0; //getopt()のエラーメッセージを無効にする。
~
~ 以下省略
~
```
**入力例9：-o aaa**

```text:
$ ./sample -o aaa
./sample: illegal option -- o
Usage: ./sample [-f] [-g] [-h argment] arg1 ...
```
opterrの値を変更しない場合(=1)、getopt()は上のようなエラーメッセージを標準エラー出力に表示する。

##注意点
* sample.cのように`getopt() != -1`を条件にループさせた場合、引数の後にオプションを与えてしまったら、オプションを引数として受け取ってしまう。(入力例5を参照)
* 引数を取るオプションのすぐ後にオプションを与えてしまった場合、そのオプション自体を引数として取ってしまうため、思わぬバグが発生する可能性がある。(入力例６を参照)
* ハイフン２つと単語で構成されるオプションは使えない。(例：--name)
* 環境によってgetopt関数の仕様が違う可能性があるため、まずは動作を確認すること。

##getopt_long関数とgetopt_long_only関数について
getopt関数では、ハイフン２つと単語で構成されるオプションは使えないが、これを使えるようにしたのがgetopt_long関数とgetopt_long_only関数である。
この２つの関数は、getopt関数の機能に加えて、"--name"といったオプションも判別できるようになっている。それぞれの違いはここでは省略する。 
後日、これらの関数についての記事も投稿したい。

#環境

```
$ gcc -v
Configured with: --prefix=/Applications/Xcode.app/Contents/Developer/usr --with-gxx-include-dir=/usr/include/c++/4.2.1
Apple LLVM version 7.3.0 (clang-703.0.31)
Target: x86_64-apple-darwin15.5.0
Thread model: posix
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin
```

#参考
* [getopt関数の利用 コマンドラインオプションの処理 - 碧色工房-blue studio-](http://www.mm2d.net/main/prog/c/getopt-02.html)
* [コマンドライン引数の取得とgetopt - ファイヤープロジェクト](http://www.fireproject.jp/feature/c-language/basic-library/getargs.html)
* [オプションの取り方を勉強しよう！ - 初心者の初心者による初心者のためのC言語講座
](http://www.geocities.co.jp/SiliconValley-Cupertino/4084/Cprogram/getopt.html)
* [Man page of GETOPT](https://linuxjm.osdn.jp/html/LDP_man-pages/man3/getopt.3.html)

こちらのサイトを参考に書かせていただきました。ありがとうございましたm(_ _)m

# 追記：コメント

---

2018-10-29 16:19
> 参考にさせていただきました。
> 丁寧でわかりやすかったです。
>
> 一つ問題が発生してしまったのでアドバイスをいただきたいです。
オプション引数とは別に引数に整数を取るプログラムに組み込もうとしたところ、while文の条件を満たさないと判別されその後のオプション引数が無視されてしまいます。
>
> 具体的には`./sample.out 4 -f`のようなプログラムを実行したいときに`-f`が無視されてしまいます。
このような要求を解決するようなコードを加えるためにはどのようにすればいいでしょうか。

---
2018-11-01 14:07 返信したコメント

コメントありがとうございます。返信が遅れてしまいすみません。

`./sample.out 4 -f`を実行したところ、私の環境(gcc-8 (Homebrew GCC 8.2.0) 8.2.0)でも`-f`は無視されました。

まず、getopt関数は受け取った引数(`argv`)の中身を適切な順番に並び替える機能があるらしいです。

> getoptはargvの並び順を変更し、オプション以外の引数は、末尾にまとめるように動作する。 また、グローバルスコープの変数 optind は、 getopt が次に処理する引数のindexを示している。 オプションがすべて処理し終わった時点での optind は、オプション以外の引数の先頭を示している。 （オプション以外の引数がない場合は当然末尾を指すことになる） そこで、サンプルプログラムでは残りの引数を表示させている。
>
>[getopt関数の利用 - コマンドラインオプションの処理 - 碧色工房](https://www.mm2d.net/main/prog/c/getopt-02.html)

しかし、`./sample.out 4 -f`のように実行すると、実際は、先に`4`が解析され、後ろの`-f`をオプションではなく非オプションとして認識します。調べたところ、以下の記事を見つけました。

> optionとnon-option argumentの順番を入れ替えても動作するようにするために、argument listの並び替えを行う場合があります。
>
>この動作は実装依存です。実装によっては並び替えしないgetoptもあります。
>
>[getoptを使う - Qiita](https://qiita.com/tobira-code/items/24e583c30f07c4853f8f#argument-listの並び替え)

どうやら、`argv`の並び替えはコンパイラ依存の動作のようです。

そこで、並び替えに頼らずに、引数の数だけ`getopt()`を実行してみたのですが、オプションを受け取らなかった(`4`を解析した)時点で、解析は終わるようで、`-f`を解析しても`-1`を返してきました。`getopt()`は`argv`の中身がコマンド名 オプション 非オプションの順番である必要があるようです。

私が思いついたあなたができることとしては、

- `argv`を適切にソートする。(もっとも、これをするくらいなら`getopt()`を使う旨みはなくなる)
- `./sample.out 4 -f`のような実行を禁止し、`./sample.out -f 4`で妥協する。
- もしあなたがC++で実装可能なら、`getodpt()`を使わず、ほかのライブラリを利用する。([参考](https://heavywatal.github.io/cxx/getopt.html))
- `getopt()`に頼らない。

のどれかだと思います。

解決策を見つけることができなくてすみません。
また、上の情報の根拠となるソースを見つけることができませんでしたので、正しくない可能性があります。

もし、解決することができたら、記事にしていただくか、コメントでご報告いただけると幸いです。

---
2018-11-07 09:32

> 返信おそくなり申し訳ありません。
>
> 丁寧にありがとうございます。非常に助かります。
>
> 本質的な問題ではなかったので今回はgetopt()に頼らず実装してみようかと思います。
>
> また新たな発見がありましたらコメントさせていただきます。


<!-- 記事終わり線 -->
---

<!-- ここに脚注が来る -->
