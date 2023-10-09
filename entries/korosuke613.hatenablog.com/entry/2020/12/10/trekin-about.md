---
Title: Trello の内容を kintone アプリに同期するシステムを作ったよ
Category:
- 生産性向上
- プログラミング
- JavaScript/TypeScript
- サイボウズ
- ノウハウ
Date: 2020-12-11T09:00:00+09:00
URL: https://korosuke613.hatenablog.com/entry/2020/12/10/trekin-about
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613662111838
---

<!-- ここに導入を書く -->
こんにちは。生産性向上チームの平木場です。

[kintone](https://kintone.cybozu.co.jp/) はすばらしいサービスでさまざまな業務効率の改善を助けてくれます。例えば、プロセス管理等の機能を利用することで、タスク管理アプリを作ることが可能です。しかし、タスク管理ツールとしてはカンバン方式でタスク管理ができるサービスである Trello を使う方が便利な場合が多いと思います。

なので僕が所属するチームでは、**kintone を使いつつ、タスク管理には [Trello](https://trello.com/ja) を使っています**。しかし、kintone の情報は kintone に、Trello の情報は Trello に閉じ込められてしまうので、「**情報がバラバラになってしまう**」「**情報が他チームから見えなくなってしまう**((チームで使っているTrelloボードを閲覧する権限がないため))」という問題も起こってしまっていました。

今回は、上記のような問題を解決するために **Trello の内容を kintone アプリに同期するシステム Trekin を作った話**をします。

この記事は[kintone 2 Advent Calendar 2020](https://qiita.com/advent-calendar/2020/kintone2) の 11 日目の記事です 🎅🎂

<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]

<!-- ここに広告が入る -->
---

# ざっくりこんな感じ
例えば、下のようなカードを作ったり編集すると...
<img width="70%" src="https://cdn-ak.f.st-hatena.com/images/fotolife/k/korosuke613/20201210/20201210191608.png">

下のように kintone アプリに反映されます。
<img width="70%" src="https://cdn-ak.f.st-hatena.com/images/fotolife/k/korosuke613/20201210/20201210191633.png">


アプリコードを有効にすることで、同じ環境から簡単にレコードに飛ぶことができます。kintone アプリのレコードにタスクの情報が記載されているので、必要な情報にアクセスしやすくなります。

<img width="70%" src="https://cdn-ak.f.st-hatena.com/images/fotolife/k/korosuke613/20201210/20201210192155.png">



# Trekin でできること

Trekin は以下の機能を持っています。

- Trello のカード、リスト、ラベルの作成、更新等の内容を kintone アプリに転記する
- 転記された kintone アプリのレコードのリンクを転記元である Trello カードに添付する
- 転記された kintone アプリのレコード ID を転記元である Trello カードのカード名に追記する
- カードが Done のリストに配置されたときの時間を記録する
- 正規表現や文字数で転記しないカードを設定する

また、Trekin を使うことで以下のことを期待できます。

- kintone に情報を集約できる
- チーム外の人にチームがいま何に取り組んでいるか知ってもらえる
- 期限が設定されたカードにアサインされた人に kintone 上で通知が飛ばせる
- [アプリコード機能](https://jp.cybozu.help/k/ja/user/app_settings/app_othersettings/appcode.html)を使うことで kintone 上から容易に目的の Trello カードへアクセスできる



# Trekin の仕組み
Trello は [API を使って Webhook を登録する](https://developer.atlassian.com/cloud/trello/guides/rest-api/webhooks/)ことができます。
Trekin は Trello が Webhook で飛ばしてくるイベントの解析を解析し、適切に kintone REST API の呼び出しを行う NPM ライブラリです。

[https://github.com/korosuke613/trekin:embed:cite]

したがって、自動で Trello の内容を kintone に転記するにはサーバを用意し、Trekin を組込む必要があります。
下の構成図は AWS Lambda と Amazon API Gateway を用いて作った Trello の内容を kintone アプリに同期するシステムの例です。

<figure class="figure-image figure-image-fotolife" title="Trekin の構成図">[f:id:korosuke613:20201208131914j:plain]<figcaption>Trekin を用いた Trello の内容を kintone アプリに同期するシステムの構成例</figcaption></figure>

Trello の Webhook に API Gateway のエンドポイントを指定し、Lambda に配置した Trekin で、API Gateway から送られてくる Trello イベントを処理するという単純な構造になっています。

下の図は上のシステムの処理の流れです。

<figure class="figure-image figure-image-fotolife" title="Trekin を用いた Trello の内容を kintone アプリに同期するシステム例のシーケンス図">[f:id:korosuke613:20201208132815j:plain]<figcaption>Trekin を用いた Trello の内容を kintone アプリに同期するシステム例のシーケンス図</figcaption></figure>

Trello は Webhook で送ってくる JSON に [ActionType](https://developer.atlassian.com/cloud/trello/guides/rest-api/action-types/) というものを持たせています。
例えばカード作成を行った場合、`"ActionType": "createCard"`になります。
この ActionType が何かで分岐をしてその後の行動を決めています。（[ソースコード](https://github.com/korosuke613/trekin/blob/master/src/Worker.ts#L22)）

送られてきた JSON を紐解くと Trello 上で何が起こったのかがわかります。
例えば新しいカードを作った場合、以下のような JSON が飛んできます。
```json
{
  "action": {
    （中略）
    "data": {
      "card": {
        "id": "（略）",
        "name": "kintone Advent Calendar の記事を書く",
        "idShort": 7,
        "shortLink": "Vex35GOC"
      },
      "list": {
        "id": "（略）",
        "name": "TODO"
      },
      "board": {
        "id": "（略）",
        "name": "trello APIテスト",
        "shortLink": "Sqsia9EM"
      }
    },
    "type": "createCard",
    （中略）
  }
}
```
`data`の中に、カード、リスト、ボードに関する情報が格納されています。（イベントによって何が送られてくるかは変わります）カード名は`kintone Advent Calendar の記事を書く`、所属するリストは`TODO`、ショートリンクは`Vex35GOC`だなということがわかります。これらの情報を基に kintone アプリを操作し、カード名や所属リスト、リンクなどを登録しています。

ちなみに kintone REST API の操作には [kintone-rest-api-client](https://github.com/kintone/js-sdk/tree/master/packages/rest-api-client) を使用しています。簡単に kintone REST API を叩けるのでとても助かってます。


上のシステム構成例では AWS Lambda を使用していましたが、**Trekin は AWS Lambda のリソースに依存しないので、GCP やその他の IaaS で動かすことも可能です。**Trekin 自体の使い方は上に貼ったリポジトリの README.md に載せています。

# 導入方法（工事中）
導入方法について丁寧に解説したかったのですが、丁寧に解説しようとすると時間が足りないことに気づいたので、**今回は大まかな導入の流れとサンプルコードだけ示します...** すみません。

導入方法の丁寧な解説が完成したら改めて別記事にしようと思います。

大まかな流れは以下の通りです。

1. kintone アプリ作成（ここの説明が一番めんどい）
2. Trello API を有効化
3. AWS Lambda にデプロイ（Serverless Framework を使うのが楽）
4. Trello API で Webhook を登録
5. 利用開始

Trekin を利用しているサンプルコードはこちらです。


[https://github.com/korosuke613/trekin-sample:embed:cite]


# 実際にチームに導入してみて
8月ごろから僕の所属しているチームの Trello ボードの内容を kintone に転記するようにしています。それまでは、冒頭で挙げたような「情報を検索しづらい」「他チームからすると最近何をやっているのかわかりづらい」といった問題に加えて、「過去のタスクを探しづらい」という問題もありました。**Trekin を導入することで上の問題は解決((といってもまだまだ改善の余地があると思います。))され、可視性や追跡可能性が増した**と考えます。

他にも、タスクが生まれてから Done になるまでの時間を計算できるようになった((最近追加した機能です))ことや、一ヶ月の間でどのくらいのタスクを終えたかが kintone からわかるようになったことから、**チームの活動を測るためのメトリクスとして使える**可能性も出てきました。

kintone と Trello を使っているチームが他にもいる場合、Trekin や Trekin で得た知見を活用していけると思います。あらゆるチームに使ってもらうために、今後は導入をもっと楽にしたり、もっと堅牢なシステムにしたりしていこうと思います。

導入に関しては、特に kintone アプリ作成が一番面倒です。kintone REST API を使ってアプリ作成ができることを最近知ったので、自動でTrekin 向けアプリを作れるような CLI を作る予定です。

~~**導入方法も早めに記事にしたいです**~~

<!--
# 導入方法
上の構成のサンプルコードは[こちら](https://github.com/korosuke613/trekin-sample)です。
このコードにしたがって導入していこうと思います。

### 1. kintone アプリ作成
まず、Trello の転記先となる kintone アプリを作成しましょう。

作成するアプリは次の4つです。

- カード
- リスト
- ラベル
- メンバ

それぞれのアプリは次のフィールドを持ちます。

<div onclick="obj=document.getElementById('oritatami_part').style; obj.display=(obj.display=='none')?'block':'none';">
<a style="cursor:pointer;">▶長いのでクリックして展開してください</a>
</div>
<div id="oritatami_part" style="display:none;clear:both;">

**ラベル**

|種類|フィールドコード|説明|
|---|---|---|
|文字列（1行）|NAME|ラベル名|
|文字列（1行）|ID|リストID|
|文字列（1行）| COLOR |ラベルの色|

**リスト**

|種類|フィールドコード|説明|
|---|---|---|
|文字列（1行）|NAME|リスト名|
|文字列（1行）|ID|ラベルID|
|文字列（1行）|CLOSED|リストがアーカイブされているかどうか|

**メンバ**

|種類|フィールドコード|説明|
|---|---|---|
|文字列（1行）|NAME|Trelloユーザ名|
|文字列（1行）| TRELLO_ID |TrelloのユーザID|
|ユーザー選択|USER|kintoneユーザ|

**カード**

|種類|フィールドコード|説明|
|---|---|---|
|文字列（1行）|NAME|カード名|
|文字列（1行）|ID|カードID|
|文字列（複数行）|DESC|カードの詳細|
|リンク|LINK|カードのリンク|
|ルックアップ|LIST_LOOK|カードが所属するリストID|
|文字列（1行）|LIST|カードが所属するリスト名|
|日時|DUE|カードの期限にあたる|
|数値|DUE_REMINDER|カードの期限のリマインダー|
|文字列（1行）|CLOSED|カードがアーカイブされているかどうか|
|ユーザー選択|USER|カードにアサインされているメンバー|
|日時|DONE_TIME|カードが Done リストになった日時|
|テーブル|LABEL_TABLE|カードに付与されたラベル|
|テーブル|ATTACHMENT_TABLE|カードの添付ファイル|

テーブル(LABEL_TABLE)の中身

|種類|フィールドコード|説明|
|---|---|---|
|ルックアップ|LABEL_ID|ラベルID|
|文字列（1行）|LABEL_NAME|ラベル名|
|文字列（1行）|LABEL_COLOR|ラベルの色|

テーブル(ATTACHMENT_TABLE)の中身

|種類|フィールドコード|説明|
|---|---|---|
|文字列（1行）|ATTACHMENT_NAME|添付ファイル名|
|リンク|ATTACHMENT_LINK|添付ファイルへのリンク|

LIST_LOOK は「関連づけるアプリ = リストアプリ」、「コピー元フィールド = ID」とし、「他のフィールドのコピー」に 「LIST < NAME」 と設定すると、リストアプリを参照してリスト名をカードアプリのレコードに表示できます。

LABEL_ID は「関連づけるアプリ = ラベルアプリ」、「コピー元フィールド = ID」とし、「他のフィールドのコピー」に 「LABEL_NAME < NAME」 「LABEL_COLOR < COLOR」と設定すると、ラベルアプリを参照してラベルをカードアプリのレコードに表示できます。

</div>


最終的に出来上がるカードアプリは以下のようになります。
[f:id:korosuke613:20201210184953p:plain]

### 2. AWS Lambda にデプロイ


### 3. Trello API で Webhook 登録

-->

<!-- 記事終わり線 -->
---

<!-- ここに脚注が来る -->
