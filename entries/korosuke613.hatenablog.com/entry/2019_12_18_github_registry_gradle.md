---
Title: GitHub ActionsでGitHub Package RegistryにJavaパッケージを公開する
Category:
- CI/CD
- Java/Kotlin
- プログラミング
- ノウハウ
- 生産性向上
Date: 2019-12-18T00:05:49+09:00
URL: https://korosuke613.hatenablog.com/entry/2019_12_18_github_registry_gradle
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613485721467
---

<!-- ここに導入を書く -->

<figure class="figure-image figure-image-fotolife" title="タイトル">[f:id:korosuke613:20191217235335p:plain]</figure>

**[GitHub Actions Advent Calendar 2019](https://qiita.com/advent-calendar/2019/github-actions) 18日目の記事です。**

Javaのパッケージ(jar)はMaven Centralにアップするのが主流でした。しかし、GitHub Package Registryがリリースされて、Javaパッケージの公開のハードルが下がったような気がします。

今回は、**GitHubにtagをpushする**ことで、GitHub Actionsで**GitHub Package RegistryにJavaパッケージを自動で公開する**方法を記します。

<!-- 続きを読むのやつ -->
<!-- more -->

---

[:contents]

<!-- ここに広告が入る -->
---

# 仕組み
<figure class="figure-image figure-image-fotolife" title="公開までの流れ">[f:id:korosuke613:20191217235358p:plain]</figure>

上の画像のような仕組みで、**tagをpushしたときのみ**、自動でパッケージを公開できるように設定します。((本当はテストもすべきなのですが、今回の記事ではテストは扱いません。))

# 環境
以下の環境で検証しました。

- Java 11
- Gradle 5.2.1

# パッケージをGitHub Actionsで公開する

## 今回公開するパッケージ
今回公開するパッケージのリポジトリは[コチラ](https://github.com/korosuke613/java-plugin-publish-github-example)です。

[Sample](https://github.com/korosuke613/java-plugin-publish-github-example/blob/master/src/main/java/com/github/korosuke613/example/publish/Sample.java)という簡単なクラスを公開して、別のプロジェクトでMavenやGradleでSampleクラスを使えるようにします((普段はKotlinを書いており、非常に久々にJavaを弄ったので、えらい大変でした...))。

<figure class="figure-image figure-image-fotolife" title="公開したパッケージ">[f:id:korosuke613:20191217201619p:plain]</figure>

## 公開するための設定

### settings.gradle
*ファイル全体は[こちら](https://github.com/korosuke613/java-plugin-publish-github-example/blob/master/settings.gradle)*

#### artifactIdの設定

<pre class="prettyprint linenums">
rootProject.name = 'publish'
</pre>

`rootProject.name`は公開後、`artifactId`になります。

*com.github.korosuke613.example.**publish**:v1.0.1*の太字部分です。

### build.gradle
[GitHub公式のドキュメント](https://help.github.com/en/github/managing-packages-with-github-packages/configuring-gradle-for-use-with-github-packages)を参考にしています。

<pre class="prettyprint  linenums">
plugins {
    id 'java'
    id "maven-publish"  // 公開に必要なプラグイン
}

group 'com.github.korosuke613.example'

// GitHub Action上でタグ名がバージョンとなる
version = System.getenv("GRADLE_PUBLISH_VERSION") ?: "1.0-SNAPSHOT"

sourceCompatibility = 11

repositories {
    mavenCentral()
}

// 公開設定
publishing {
    repositories {
        maven {
            name = "GitHubPackages"
            url = uri("https://maven.pkg.github.com/korosuke613/java-plugin-publish-github-example")
            credentials {
                username = System.getenv("GITHUB_PACKAGE_USERNAME")
                password = System.getenv("GITHUB_PACKAGE_TOKEN")
            }
        }
    }
    publications {
        gpr(MavenPublication) {
            from(components.java)
        }
    }
}
</pre>

*ファイル全体は[こちら](https://github.com/korosuke613/java-plugin-publish-github-example/blob/master/build.gradle)*


#### プラグインの追加

<pre class="prettyprint  linenums">
plugins {
    id 'java'
    id "maven-publish"
}
</pre>

GitHub Package Registryに公開するために、`maven-publish`プラグインをインストールする必要があります。


#### groupIdの設定

<pre class="prettyprint  linenums">
group 'com.github.korosuke613.example'
</pre>

`group`は公開後、`groupId`になります。

***com.github.korosuke613.example**.publish:v1.0.1*の太字部分です。((僕は、`groupId`と`artifactId`をどこで区切れば良いのかいまいちよくわかっていません。だれか教えてください))


#### versionの設定

<pre class="prettyprint  linenums">
version = System.getenv("GRADLE_PUBLISH_VERSION") ?: "1.0-SNAPSHOT"
</pre>

`System.getenv()`で環境変数を取得できます。`version`に取得した環境構築の値を代入するのですが、もし、指定した環境変数がなければ、`1.0-SNAPSHOT`を代入するようにしています。

GitHub Actionsでワークフローを実行中に、`GRADLE_PUBLISH_VERSION`にタグ名を代入します。

`version`は公開後、そのまま`version`になります。

*com.github.korosuke613.example.publish:**v1.0.1***の太字部分です。

#### 公開先と認証情報の設定

<pre class="prettyprint  linenums">
publishing {
    repositories {
        maven {
            name = "GitHubPackages"
            url = uri("https://maven.pkg.github.com/korosuke613/java-plugin-publish-github-example")
            credentials {
                username = System.getenv("GITHUB_PACKAGE_USERNAME")
                password = System.getenv("GITHUB_PACKAGE_TOKEN")
            }
        }
    }
    publications {
        gpr(MavenPublication) {
            from(components.java)
        }
    }
}
</pre>

- `url`には`https://maven.pkg.github.com/GitHubユーザ名/リポジトリ名`を設定します。
- `username`にはGitHubユーザ名を設定します。
- `password`にはGitHubのパーソナルアクセストークンを設定します。(**GitHubのパスワードではありません！**)もし、自分のPCで`gradle publish`するなら、パーソナルアクセストークンを作成する必要がありますが、GitHub Actionsでは、`secrets.GITHUB_TOKEN`で代用できるので、パーソナルアクセストークンを改めて作成する必要はありません。

`username`と`password`の設定で使っている環境変数は、GitHub Actionsでワークフローを実行中に設定します。

### publish.yaml
GitHub Actionsのワークフローの設定です。

<pre class="prettyprint  linenums">
name: Publish

on:
  push:
    tags:
      - v*  # 'v'から始まるタグをpushしたときのみワークフローを実行する

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v1
      - name: Set up JDK 11
        uses: actions/setup-java@v1
        with:
          java-version: 11
      - name: Get the tag version  # タグ名を保存する
        id: get_version
        run: echo ::set-output name=VERSION::${GITHUB_REF/refs\/tags\//}
      - name: Publish
        env:
          GITHUB_PACKAGE_USERNAME: korosuke613  # ユーザー名
          GITHUB_PACKAGE_TOKEN: ${{ secrets.GITHUB_TOKEN }}  # トークン
          GRADLE_PUBLISH_VERSION: ${{ steps.get_version.outputs.VERSION }} # タグ名
        run: ./gradlew publish
</pre>

*ファイル全体は[こちら](https://github.com/korosuke613/java-plugin-publish-github-example/blob/master/.github/workflows/publish.yml)*

#### トリガー設定

<pre class="prettyprint  linenums">
on:
  push:
    tags:
      - v* 
</pre>

このワークフローを実行する条件を設定しています。

この設定は、**vから始まる名前のtagをpushしたらトリガーする**というものになります。この設定をしていることで、無駄にパッケージを公開せずにすみます。

[参考](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/workflow-syntax-for-github-actions#on)

#### tag名の保存

<pre class="prettyprint">
      - name: Get the tag version
        id: get_version
        run: echo ::set-output name=VERSION::${GITHUB_REF/refs\/tags\//}
</pre>

タグ名を保存しています。

[こちらのサイト](https://github.community/t5/GitHub-Actions/How-to-get-just-the-tag-name/m-p/32167/highlight/true#M1027)を参考にしました。

#### パッケージ公開

<pre class="prettyprint  linenums">
      - name: Publish
        env:
          GITHUB_PACKAGE_USERNAME: korosuke613 
          GITHUB_PACKAGE_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          GRADLE_PUBLISH_VERSION: ${{ steps.get_version.outputs.VERSION }} 
        run: ./gradlew publish
</pre>

実際に`./gradlew publish`を実行して、GitHub Package Registryにパッケージを公開する設定です。

`env`ブロックでは環境変数を設定することができます。

##### 認証情報

`GITHUB_PACKAGE_USERNAME `、`GITHUB_PACKAGE_TOKEN`は公開に必要な認証情報となります。<a href="#公開先と認証情報の設定">(参照)</a>

`secrets.GITHUB_TOKEN`は「**GitHub Actionsの代理で認証を受けるために利用できるトークン**」(([公式ドキュメント曰く](https://help.github.com/en/actions/automating-your-workflow-with-github-actions/authenticating-with-the-github_token)))です。GitHub Actions上で参照できるため、GitHub周りの認証で楽できます。

##### version

`GRADLE_PUBLISH_VERSION`は、versionの設定に必要なtag名となります。<a href="#versionの設定">(参照)</a>

`steps.get_version.outputs.VERSION`は、「<a href="#versionの設定">tag名の保存</a>」で設定したtag名を参照しています。

## 実際にtagをpushする
実際にtag(v0.0.5)をpushして、Actionsのページに行くと、`Publish`というワークフローが実行されていることが確認できます。([リンク](https://github.com/korosuke613/java-plugin-publish-github-example/commit/6784991ff0ff10b7fcb876fca9c720ea69cb6b58/checks?check_suite_id=362333788))((実行するたびにGradleをダウンロードしてしまうので、Cache機能なんかを使って実行速度をはやめたいですね))

<figure class="figure-image figure-image-fotolife" title="Actions画面">[f:id:korosuke613:20191217222432p:plain]</figure>

画像のように、`Task :publish`が正常に実行されていたら、パッケージがアップロードされているはずです。([リンク](https://github.com/korosuke613/java-plugin-publish-github-example/packages/82089))

<figure class="figure-image figure-image-fotolife" title="公開されたパッケージ">[f:id:korosuke613:20191217222851p:plain]</figure>

# パッケージを利用する
公開したパッケージを利用しましょう。

利用するためのソースコードは[こちら](https://github.com/korosuke613/java-plugin-publish-github-use-example)に上げています。

## 利用するための設定

### build.gradle

<pre class="prettyprint  linenums">
plugins {
    id 'java'
}

group 'com.github.korosuke613.example'
version '1.0-SNAPSHOT'

sourceCompatibility = 11

repositories {
    maven {
        name = "GitHubPackages"
        url = uri("https://maven.pkg.github.com/korosuke613/java-plugin-publish-github-example")
        credentials {
            username = System.getenv("GITHUB_PACKAGE_USERNAME")
            password = System.getenv("GITHUB_PACKAGE_TOKEN")
        }
    }
    mavenCentral()
}

dependencies {
    implementation "com.github.korosuke613.example:publish:v1.0.1"
    implementation "com.github.korosuke613:pict4java:v2.0.0"
}
</pre>

#### GitHub Package Registryにログインする

<pre class="prettyprint  linenums">
repositories {
    maven {
        name = "GitHubPackages"
        url = uri("https://maven.pkg.github.com/korosuke613/java-plugin-publish-github-example")
        credentials {
            username = System.getenv("GITHUB_PACKAGE_USERNAME")
            password = System.getenv("GITHUB_PACKAGE_TOKEN")
        }
    }
    mavenCentral()
}
</pre>

**パッケージを公開したのと同じように、GitHub Package Registryにログインする必要があります。**

**Publicなリポジトリでパッケージを公開していたとしても、認証作業が必要です。**

なぜこういう作りになっているのかはわかりません。実際、GitHub Communityで議論になっています。

[https://github.community/t5/GitHub-Actions/docker-pull-from-public-GitHub-Package-Registry-fail-with-quot/td-p/32782:embed:cite]

`url`には、<a href="#公開先と認証情報">先ほどと同じようなURL</a>を設定していますが、**設定したURLとは別のリポジトリのパッケージも、問題なく利用できたことから、別のリポジトリのURLでも良さそうです。**一回認証すれば良いということなのでしょうか。

（ここら辺は僕も良くわかっていないので、だれか教えてください...）

。

#### dependenciesに追加

<pre class="prettyprint  linenums">
dependencies {
    implementation "com.github.korosuke613.example:publish:v1.0.1"
    implementation "com.github.korosuke613:pict4java:v2.0.0"
}
</pre>

`dependencies`に先ほど公開したパッケージを記述しています。

今回は、`<groupId>:<artifactId>:<version>`というルールで記述しています。((Gradleには別の記述方法もあった気がする))


ちなみに、`com.github.korosuke613:pict4java:v2.0.0`は僕が既に同じ方法で公開している[別のパッケージ](https://github.com/korosuke613/pict4java)です。

## 実際に使ってみる
本当に使えるか確認するため、以下のソースコードを実行しました。

### Main.java

<pre class="prettyprint  linenums">
package com.github.korosuke613.example.publish.use;

import com.github.korosuke613.example.publish.Sample;

public class Main {
    public static void main(String[] args) {
        Sample sample = new Sample();

        System.out.println(sample.sum(1, 2));
        System.out.println(sample.sum("Hello ", "GitHub Package Registry"));
    }
}
</pre>

### 実行結果

<pre class="prettyprint  linenums">
$ ./gradlew run          

> Task :run
3
Hello GitHub Package Registry

BUILD SUCCESSFUL in 1s
2 actionable tasks: 1 executed, 1 up-to-date
</pre>
**ちゃんと、`3`、`Hello GitHub Package Registry`と出力されているので、正しくインポートできていることが確認できました！**

# おわりに
今回は、tagをpushすることで、Javaのパッケージを自動でGitHub Package Registryに公開してみました😎

もっと良い方法があれば、ぜひ教えてくださいm(__)m

<!-- 記事終わり線 -->

---

<!-- ここに脚注が来る -->
