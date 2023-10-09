---
Title: macOSでDockerを使って簡単にTOPPERS/EV3RTのビルド環境を構築する
Category:
- ETロボコン
- アーカイブ
- Docker
Date: 2018-05-24T00:00:00+09:00
URL: https://korosuke613.hatenablog.com/entry/2018/05/24/ev3rt-mac-docker
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613632801459
---

<!-- ここに導入を書く -->
**この記事は、[https://qiita.com/Shitimi_613/items/4b9ef44bafd5a5154fc5:title]のアーカイブです。**

本稿は[LEGOマインドストーム](https://afrel.co.jp/product/ev3-introduction)で、リアルタイムOSである[TOPPERS/EV3RT](http://dev.toppers.jp/trac_user/ev3pf/wiki/WhatsEV3RT)用のプログラムをビルドする環境をmacOSでDockerを使って簡単に構築するための方法を記しています。

内容は先日書いた下の記事とほぼ同じです。

[https://korosuke613.hatenablog.com/entry/2018/01/17/ev3rt-bow-docker:embed:cite]



<!--  カスタムURLは`YYYY/MM/DD/name`の形式にする -->
<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]

---

## Dockerを用いないビルド環境の構築について
もちろんDockerを使わずに環境を構築する方法はあります。
macOSで環境を構築する場合、例えば[公式の方法](https://dev.toppers.jp/trac_user/ev3pf/wiki/DevEnvMac)はGNUツールチェーンをインストールしています。
1からインストールしても良いのですが、Dockerを使うと様々な利点があるので、今回Dockerを使った環境構築方法を記すことにしました。

# 追記：2020/01/11
VS CodeのRemote Container機能を使って、ビルド環境だけじゃ無く開発環境も簡単に構築する方法を記事に書きました。

[https://korosuke613.hatenablog.com/entry/2019/12/08/205203:embed:cite]



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

ETrobo-Dockerを使うためにはDockerを使えるようになる必要があります。macOSではDocker Toolboxをインストールすることで実現できる。

### Docker CEをインストールする
Dockerを使えるようにするためにDocker CEをインストールします。

1. [Docker Store](https://store.docker.com/editions/community/docker-ce-desktop-mac)の`Get Doccker`をクリックしてディスクイメージ(img)をダウンロードします。
2. `Docker.img`を実行すると、以下のようなウィンドウが立ち上がるので、`Docker.app`を`Applications`ディレクトリに移動します。

[f:id:korosuke613:20200926200035p:plain]

### Dockerを起動する
`/Applications/Docker.app`起動すると、上部のメニューバーにDocker鯨のアイコンが出てきます。クリックすると、以下のようなプルダウンメニューが出てきます。プルダウンメニューの一番上の項目が`Docker is running`になれば、Dockerの起動は完了です。

[f:id:korosuke613:20200926200049p:plain]

しっかり動くか確認するために、ターミナルを開いて以下のコマンドを実行しましょう。

```bash:~
$ docker run hello-world
```

[f:id:korosuke613:20200926200128p:plain]

上のような文字が出力されればインストールは完了！

## EV3RTのサンプルコードをダウンロードする
**※既にソースコードを持っている方は[次のセクション](#ソースコードをビルドする)に進んでください**

[f:id:korosuke613:20200926200146p:plain]

1. [TOPPERSのEV3のページ](http://dev.toppers.jp/trac_user/ev3pf/wiki/Download#%E3%83%80%E3%82%A6%E3%83%B3%E3%83%AD%E3%83%BC%E3%83%89)で`ev3rt-beta7-1-release.zip`をダウンロードし、解凍します。
2. 解凍したディレクトリの中にある`hrp2.tar.gz`を解凍[^3]し、解凍した`hrp2/sdk/workspace`ディレクトリをユーザのホームディレクトリ直下`/Users/(ユーザ名)/workspace`にコピーします。

## ソースコードをビルドする
ターミナルで目的のソースコードがあるフォルダに移動する。
`/Users/(ユーザ名)/workspace`以下にソースコードがある場合、以下のコマンドで移動できる。

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

[^1]: 事実、2017年10月にBowを使った方法で複数の学生に環境構築をさせたところ、当時はWindowsのバージョンによってUbuntuのバージョンが異なり、謎のエラーでビルドができない学生が出て、大変なことになりました。
[^2]: `Ctrl + Shift + Esc` キーを押すか、タスクバーの空白の領域を右クリックし、[タスク マネージャー] をクリックして、タスクマネージャを起動できます。
[^3]: `tar.xz`の解凍ができない場合は[こちら](https://armadillo.atmark-techno.com/howto/tar_xz-extract)を参考にしてください。
[^4]: 私の所属する研究室では毎年1年生にMindstormsで演習をしており、毎回各学生の環境構築で苦戦します。そこでDockerを用いることにしました。



<!-- 記事終わり線 -->

<!-- ここに脚注が来る -->
