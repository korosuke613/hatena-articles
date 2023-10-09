---
Title: Windows10 HomeでDockerを使って簡単にTOPPERS/EV3RTのビルド環境を構築する
Category:
- ETロボコン
- アーカイブ
- Docker
Date: 2018-01-17T00:00:00+09:00
URL: https://korosuke613.hatenablog.com/entry/2018/01/17/ev3rt-bow-docker
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613632796616
---

<!-- ここに導入を書く -->
**この記事は、[https://qiita.com/Shitimi_613/items/35b3a30ba55ee6f8e01d:title]のアーカイブです。**

本稿は[LEGOマインドストーム](https://afrel.co.jp/product/ev3-introduction)で、リアルタイムOSである[TOPPERS/EV3RT](http://dev.toppers.jp/trac_user/ev3pf/wiki/WhatsEV3RT)用のプログラムをビルドする環境をWindows10 HomeエディションでDockerを使って簡単に構築するための方法を記しています。

<!--  カスタムURLは`YYYY/MM/DD/name`の形式にする -->
<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]

---

# はじめに
本稿は[LEGOマインドストーム](https://afrel.co.jp/product/ev3-introduction)で、リアルタイムOSである[TOPPERS/EV3RT](http://dev.toppers.jp/trac_user/ev3pf/wiki/WhatsEV3RT)用のプログラムをビルドする環境をWindows10 HomeエディションでDockerを使って簡単に構築するための方法を記しています。

Windows10 Pro以上を持っている方はDocker for Windowsをインストールして使う方が楽です。[[手順/EV3RTのサンプルコードをダウンロードする](#ev3rtのサンプルコードをダウンロードする)]までスキップしてください。

[macOSの方はこちら](https://qiita.com/Shitimi_613/items/4b9ef44bafd5a5154fc5)

## Dockerを用いないビルド環境の構築について
もちろんDockerを使わずに環境を構築する方法はあります。
Windowsで環境を構築する場合、例えば[公式の方法](https://dev.toppers.jp/trac_user/ev3pf/wiki/DevEnvWin)ではCygWinを使っています。他にも、[Bash on Windows[BoW]を使う方法](https://qiita.com/Shitimi_613/items/09bec911048e55285868)もあります。
しかし、CygWin・BoWは時間がかかり、バージョンによって動作に大きな差[^1]がでることから、今回Dockerを使った環境構築方法を記すことにしました。


# 予備知識

## Docker
> Dockerはコンテナ型の仮想化環境を提供するオープンソースソフトウェアである。VMware製品などの完全仮想化を行うハイパーバイザ型製品と比べて、ディスク使用量は少なく、仮想環境 (インスタンス) 作成や起動は速く、性能劣化がほとんどないという利点を持つ。
>
> *[WikipediaのDockerの記事より](https://ja.wikipedia.org/wiki/Docker)*

仮想化技術の一つで、例えば仮想マシン(VirtualBoxなど)と比べて、起動が速い・メモリ消費量が少ない・移植性が高いなどといったメリットが存在します。
必要なミドルウェアがインストール済の環境を手軽に用意できることと上記のメリットから、多くの方に利用されています。

**参考**

* [【入門】Dockerとは？使い方と基本コマンドを分かりやすく解説します](https://www.kagoya.jp/howto/rentalserver/docker/)
* [今からでも間に合うDockerの基礎。コンテナとは何か、Dockerfileとは何か。Docker Meetup Tokyo #2](http://www.publickey1.jp/blog/14/dockerdockerfiledocker_meetup_tokyo_2.html)

## ETrobo-Docker
TOPPERS/EV3RTのビルド環境をラッピングしたUbuntuベースのDockerイメージです。
EV3RTをビルドするための環境が用意されています。今回はこれを利用します。
詳細は[こちら](https://hub.docker.com/r/korosuke613/etrobo-docker/)


# 手順
## Dockerを使えるようにする
**※既にDockerがインストールされている場合は[次のセクション](#ev3rtのサンプルコードをダウンロードする)にお進みください**

ETrobo-Dockerを使うためにはDockerを使えるようになる必要がある。Windows10のHomeエディションではDocker Toolboxをインストールすることで実現できる。

### Intel VT-X/AMD-vが有効かどうかを確認する
Dockerを動かすためには仮想化ハードウェア拡張を有効にする必要がある。有効になっているかどうかを確認する。
有効でない場合は、BIOSより有効にする。（[参考](http://www.sony.jp/support/vaio/products/manual/vpcx11/contents/03_rec/bios/04/04.html)）

1. タスクマネージャを起動[^2]する。
2. `詳細(D)`が表示されている場合はそれをクリックする。
3. `パフォーマンス`タブをクリックする。
4. CPUの欄の`仮想化`が`有効`になっているか確認する。

[f:id:korosuke613:20200926194906p:plain]

### Docker Toolboxをインストールする
Dockerを使えるようにするためにDocker Toolboxをインストールする。
以下の5つがインストールされる。

* Docker Client for Windows
* Docker Machine for Windows
* Kitematic for Windows(Alpha)
* VirtualBox
* Git for Windows

1. [Docker公式ページ](https://docs.docker.com/toolbox/toolbox_install_windows/)の`Get Doccker Toolbox for Windows`をクリックしてインストーラをダウンロードする。
2. インストーラを実行し、画面にしたがってインストールする。途中にあるチェックボックスは理由がない限りデフォルトで構わない。

[f:id:korosuke613:20200926194924p:plain]

インストールが完了すると、`Docker Quickstart Terminal`と`Kitematic`のショートカットが生成されています。

* `Docker Quickstart Terminal`はDockerVMを素早く起動することができ、起動後はそのままMINGW64ターミナルとして利用できる。
* `Kitematic`はDockerをGUIで操作するツール。本稿では扱わない。[(参考)](https://qiita.com/akiko-pusu/items/d07751a6bf0ce6df44ce)

### Docker Quickstart Terminalを起動する
デスクトップにできた`Docker Quickstart Terminal`をダブルクリックすると、ターミナルが起動する。インストール後の初回起動時はDockerVMが立ち上がるまで少し時間がかかる。以下の鯨の画面になればDockerが使えるようになる。

[f:id:korosuke613:20200926194945p:plain]

しっかり動くか確認するために、以下のコマンドを実行する。

```bash:~
$ docker run hello-world
```

[f:id:korosuke613:20200926195005p:plain]

上のような文字が出力されればインストールは完了！

## EV3RTのサンプルコードをダウンロードする
**※既にソースコードを持っている方は[次のセクション](#ソースコードをビルドする)に進んでください**

[f:id:korosuke613:20200926195022p:plain]

> 平成30年10月8日(月)追記：
> 現在の最新版はβ7-2です。

1. [TOPPERSのEV3のページ](http://dev.toppers.jp/trac_user/ev3pf/wiki/Download#%E3%83%80%E3%82%A6%E3%83%B3%E3%83%AD%E3%83%BC%E3%83%89)で`ev3rt-beta7-2-release.zip`をダウンロードし、解凍する。
2. 解凍したフォルダの中にある`hrp2.tar.gz`を解凍[^3]し、解凍した`hrp2`フォルダの中の`sdk`フォルダの中の`workspace`フォルダをユーザのホームディレクトリ直下`C:\Users\(ユーザ名)\workspace`にコピーする。

Docker Quickstart TerminalのホームディレクトリはWindowsのホームディレクトリと同じなので、ホームディレクトリ直下にワークスペースがあると後々楽になる

## ソースコードをビルドする
先ほど立ち上げたDocker Quickstart Terminalで目的のソースコードがあるフォルダに移動する。
`C:\Users\(ユーザ名)\workspace`以下にソースコードがある場合、以下のコマンドで移動できる。

```bash:~
$ cd ~/workspace
```

上のセクションでサンプルコードをダウンロードしている場合、`ls`コマンドで以下のフォルダがあることが確認できる。

[f:id:korosuke613:20200926195050p:plain]

ビルドしたいフォルダに移動する。今回は`ev3way-cpp`をビルドする。

```bash:~/workspace
$ cd ev3way-cpp
```

ビルドには、[上記](#etrobo-docker)で紹介した、TOPPERS/EV3RTのビルド環境をラッピングしたUbuntuベースのDockerイメージの[ETrobo-Docker](https://hub.docker.com/r/korosuke613/etrobo-docker/)を利用する。
以下のコマンドを実行する。初回はイメージをプルする必要があるため時間がかかる。

```bash:~/workspace/ev3way-cpp
$ docker run -v $PWD:/home/hrp2/sdk/workspace/product korosuke613/etrobo-docker
```

> 平成30年8月9日(木) 追記
> `$ docker run -v $PWD:/home/hrp2/sdk/workspace/src korosuke613/etrobo-docker`
> を
> `$ docker run -v $PWD:/home/hrp2/sdk/workspace/product korosuke613/etrobo-docker`
> に変更しました。

[f:id:korosuke613:20200926195110p:plain]

このような画面になったらビルド成功！
生成された実行ファイルは、実行したフォルダに`app`という名前で保存されている。

## Mindstormsで動かしてみる
生成した実行ファイルをMindstormsにコピーすることでMindstormsでプログラムを実行できる。

# おわりに
本稿の方法だとEV3RTのビルド環境を簡単に素早く構築できます。
今回はWindowsでの説明でしたが、Dockerが使えるなら他のOSでも同様にビルド環境を構築できます。
また、ローカル環境を汚さないので、不要になったらDockerイメージを消すだけで済みます。
マインドストームを用いた教育で、各生徒のノートパソコンを使う場合[^4]などにご活用ください。


<!-- 記事終わり線 -->

<!-- ここに脚注が来る -->
[^1]: 事実、2017年10月にBowを使った方法で複数の学生に環境構築をさせたところ、当時はWindowsのバージョンによってUbuntuのバージョンが異なり、謎のエラーでビルドができない学生が出て、大変なことになりました。
[^2]: `Ctrl + Shift + Esc` キーを押すか、タスクバーの空白の領域を右クリックし、[タスク マネージャー] をクリックして、タスクマネージャを起動できます。
[^3]: `tar.xz`の解凍ができない場合は[こちら](https://armadillo.atmark-techno.com/howto/tar_xz-extract)を参考にしてください。
[^4]: 私の所属する研究室では毎年1年生にMindstormsで演習をしており、毎回各学生の環境構築で苦戦します。そこでDockerを用いることにしました。
