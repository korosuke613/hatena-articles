---
Title: VDM++における基本データ型(Basic Data Types)
Category:
- VDM
- プログラミング
Date: 2020-02-09T23:54:25+09:00
URL: https://korosuke613.hatenablog.com/entry/2020/02/09/vdmpp-basic-data-types
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613509228606
---

<!-- ここに導入を書く -->

VDM++は**形式仕様記述言語**と呼ばれる言語で、仕様を記述するための言語です。

本記事はVDM++の型の中でも基本となる、基本データ型(Basic Data Types)の簡単なまとめです。

[Overture Toolのサイト](http://overturetool.org/documentation/manuals.html)で公開されている、[VDM-10 Language Manual](http://raw.github.com/overturetool/documentation/master/documentation/VDM10LangMan/VDM10_lang_man.pdf)を参考にしています。

<!-- 続きを読むのやつ -->
また、本記事は、自分が以前書いた[VDM++における型定義](https://qiita.com/Shitimi_613/items/24ed62480fe8247b476b)という記事をリファインした記事です。

<!-- more -->

---

**目次**

[:contents]

<!-- ここに広告が入る -->
---

# 2種類の型

VDM++には、プログラミング言語でいう**型**が存在しており、大きく分けて基本データ型と合成型で構成されています。以下が簡単な説明です。

- **基本データ型(Basic Data Types)** : 基本的で単純な型
- **合成型(Compound Types)** : 型（基本型か合成型）の組み合わせによってできる型。

# 基本データ型(Basic Data Types)
基本的で単純な型です。以下の5種類の型があります。

- **ブール型(The Boolean Type)**
- **数値型(The Numeric Types)**
- **文字型(The Character Type)**
- **引用型(The Quote Type)**
- **token型(The Token Type)**

## ブール型(The Boolean Type)
真理値を扱う型です。

**情報**

|型名|シンボル  |意味  |取り得る値  |
|---|---|---|---|
|boolean| `bool`  |真理値  | `true`, `false`  |

**利用できる演算子**

以下の`a`と`b`は任意のbool式です。

|演算子|名前|型|
|---|---|---|
|`not b`|否定(Negation)|`bool` → `bool`|
|`a and b`|論理積(Conjunction)|`bool` * `bool` → `bool`|
|`a or b`|論理和(Disjunction)|`bool` * `bool` → `bool`|
|`a => b`|含意(Implication)|`bool` * `bool` → `bool`|
|`a <=> b`|同値(Biimplication)|`bool` * `bool` → `bool`|
|`a = b`|等しい(Equality)|`bool` * `bool` → `bool`|
|`a <> b`|等しくない(Inequality)|`bool` * `bool` → `bool`|

## 数値型(The Numeric Types) 
数字を扱う型です。5種類の型で構成されています。

**情報**

|型名|シンボル  |意味  |取り得る値  |
|---|---|---|---|
|real| `real` |実数 | 全ての数 |
|rational| `rat` |有理数 | ..., `-12.78356`, ... , `0`, ..., `3`, ..., `1726.34`, ...|
|integer| `int` |整数 | ..., `-2`, `-1`, `0`, `1`, ... |
|natural| `nat`  |自然数  |`0`, `1`, `2`, ...  |
|positive natural| `nat1`  |正の自然数  | `1`, `2`, `3`, ...  |

**利用できる演算子**

以下の`x`と`y`は任意の数値です。

|演算子|名前|型|
|---|---|---|
| `-x`      | マイナス(Unary minus)           | `real` → `real`          |
| `abs x`   | 絶対値(Absolute value)          | `real` → `real`          |
| `floor x` | 切り捨て(Floor)                 | `real` → `int`           |
| `x + y`   | 加法(Sum)                       | `real` * `real` → `real` |
| `x - y`   | 減法(Difference)                | `real` * `real` → `real` |
| `x * y`   | 乗法(Product)                   | `real` * `real` → `real` |
| `x / y`   | 除法(Division)                  | `real` * `real` → `real` |
| `x div y` | 商(Integer division)            | `int` * `int` → `int`      |
| `x rem y` | `x - y * (x div y)`(Remainder)  | `int` * `int` → `int`      |
| `x mod y` | `x - y * floor(x / y)`(Modulus) | `int` * `int` → `int`      |
| `x ** y`  | 累乗(Power)                     | `real` * `real` → `real` |
| `x < y`   | 未満(Less than)                 | `real` * `real` → `bool` |
| `x > y`   | 超過(Greater than)              | `real` * `real` → `bool` |
| `x <= y`  | 以下(Less or equal)             | `real` * `real` → `bool` |
| `x >= y`  | 以上(Greater or equal)          | `real` * `real` → `bool` |
| `x = y`   | 等しい(Equal)                   | `real` * `real` → `bool` |
| `x <> y`  | 等しくない(Not equal)           | `real` * `real` → `bool` |

## 文字型(The Character Type)
単一の文字を扱う型です。 

複数のQuoteを`|`区切りで列挙することで、列挙型となります。

```
自動車の状態 = <運転中> | <停止中> | <加速中>
```

**情報**

|型名|シンボル  |意味  |取り得る値  |
|---|---|---|---|
|Char| `char`  |文字  | `'1'`, `'a'`, `'壱'` など  |

**利用できる演算子**

以下の`c1`と`c2`は任意の文字です。

|演算子|名前|型|
|---|---|---|
| `c1 = c2`   | 等しい(Equal)                   | `char` * `char` → `bool` |
| `c1 <> c2`  | 等しくない(Not equal)           | `char` * `char` → `bool` |

## 引用型(The Quote Type)
列挙型の要素を扱う型です。

|型名|シンボル  |意味  |取り得る値  |
|---|---|---|---|
|Quote| `<QuoteLit>`  |引用型  | `<運転中>`, `<停止中>`, `<加速中>`, ...  |

**＊QuoteLitは任意の文字列**

**利用できる演算子**

以下の`q`と`r`は任意の列挙型Tに属する任意の引用値です。

|演算子|名前|型|
|---|---|---|
| `q = r`   | 等しい(Equal)                   | `T` * `T` → `bool` |
| `q <> r`  | 等しくない(Not equal)           | `T` * `T` → `bool` |


## token型(The Token Type)
正直良くわかっていません。

(([栗田太郎, and 荒木啓二郎. "モデル規範型形式手法 VDM と仕様記述言語 VDM++: 高信頼性システムの開発に向けて (情報システムの信頼性・安全性)."日本信頼性学会誌 信頼性 31.6 (2009): 394-403.](https://www.jstage.jst.go.jp/article/reajshinrai/31/6/31_KJ00005654685/_pdf)))には以下のように書かれています。

> *トークンは、異なる値の無限集合であり、何らかの値ではあるが、具体的にどのような値であるのかについては、トークン型を用いる際の仕様の抽象度では、論じない型である。
仕様記述者は、トークン型の利用により、データの型や値の詳細にはあえて立ち入らずに、
概念的にデータの存在を明示したり、抽象的なデータを取り扱ったりすることが可能となる。*

「抽象的なデータですよ〜」というのを表す型って考えておきます。

**情報**

|型名|シンボル  |意味  |取り得る値  |
|---|---|---|---|
|Token| `token`  |token型 | `mk_token(5)`, `mk_token({9, 3})`, `mk_token([true, {}])`, ...  |

**利用できる演算子**

以下の`s`と`t`は任意のtokenです。

|演算子|名前|型|
|---|---|---|
| `s = t`   | 等しい(Equal)                   | `token` * `token` → `bool` |
| `s <> t`  | 等しくない(Not equal)           | `token` * `token` → `bool` |

# 次は合成型
次は、合成型の記事を書きます。

**今度こそ書きます。**

- [集合(Set Types)](https://korosuke613.hatenablog.com/entry/2020/02/12/vdmpp-compound-set-seq)
- [配列(Sequence Types)](https://korosuke613.hatenablog.com/entry/2020/02/12/vdmpp-compound-set-seq)
- 連想配列(Map Types)
- タプル(Product Types)
- 構造体(Composite Types)
- union(Union Types)
- optional(Optional Types)
- 参照(The Object Reference Type)
- ラムダ式(Function Types)

<!-- 記事終わり線 -->
---

<!-- ここに脚注が来る -->
