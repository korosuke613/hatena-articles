---
Title: 【BoW】ev3rt開発環境をWindowsで作る2
Category:
- アーカイブ
- ETロボコン
Date: 2017-10-15T00:30:00+09:00
URL: https://korosuke613.hatenablog.com/entry/2017/10/15/ev3rt-bow-2
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613632792415
---

<!-- ここに導入を書く -->
**この記事は、[https://qiita.com/Shitimi_613/items/051d04eb139043222567:title]のアーカイブです。**

<!--  カスタムURLは`YYYY/MM/DD/name`の形式にする -->
<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]

---


# はじめに
[https://korosuke613.hatenablog.com/entry/2017/10/15/ev3rt-bow-1:embed:cite]
の続きです。今回はEV3をC/C++で動かすための環境を構築します。

**[2018/01/17 追記]
[Windows10 HomeでDockerを使って簡単にTOPPERS/EV3RTのビルド環境を構築する](https://qiita.com/Shitimi_613/items/35b3a30ba55ee6f8e01d)を公開しました。本稿の方法よりも簡単にビルド環境を構築できます。**


## 対象
OS: Windows10 バージョン1703以上
※1703からUbuntuのバージョンが16.04になる為。それより前のバージョンでは`make`時にエラー(`mno-pic-data-is-relative`)が出ます。

[[参考] Windows10 Creaters Updateへの更新](https://support.microsoft.com/ja-jp/help/4028685/windows-get-the-windows-10-creators-update)

**Bash on Ubuntu on Windows(Ubuntu16.04)がインストール済であること**

## TOPPERS/EV3RTとは？

EV3RT上で動作するリアルタイムOSです。C/C++のプログラムが動きます。

**参考**
[TOPPERS/EV3RT とは？](http://dev.toppers.jp/trac_user/ev3pf/wiki/WhatsEV3RT)


# 環境構築
前回使えるようにしたBash on Ubuntu on Windowsを立ち上げてください
[参考][Linuxでの環境構築方法（Ubuntu Linux）](http://dev.toppers.jp/trac_user/ev3pf/wiki/DevEnvLinux)

## 1. ARM社のGNU Toolchainのリポジトリの導入

```bash
sudo add-apt-repository ppa:team-gcc-arm-embedded/ppa
sudo apt-get update
```

**TOPPERSの環境設定マニュアルには古いリポジトリが載っているので注意してください**

## 2. 必要なパッケージのインストール
```bash
sudo apt-get install gcc-arm-none-eabi u-boot-tools libboost-all-dev -y
```

## 3. Workspaceを作る
```bash:Windowsのユーザ名hogehogeの場合
cd /mnt/c/Users/hogehoge
mkdir ev3rt
```

## 4. ライブラリのダウンロード
[TOPPERS/EV3RT ダウンロード](http://dev.toppers.jp/trac_user/ev3pf/wiki/Download#ダウンロード)
上記のサイトを開き、「ev3rt-beta7-release.zip」をダウンロードする。

[f:id:korosuke613:20200926194112p:plain]


ダウンロードしたzipファイルを解凍し、解凍した中にある`hrp2.tar.xz`を`C:¥Users¥hogehoge¥ev3rt¥`にコピーする。（Windowsのユーザ名hogehogeの場合）

## 5. hrp2.tar.xzの解凍
```bash:/mnt/c/Users/hogehoge/ev3rt（Windowsのユーザ名hogehogeの場合）
tar Jxvf hrp2.tar.xz
```

## 6. EV3RTのコンフィギュレータのインストール
端末で，EV3RTのパッケージをインストールした場所に移動して cfg をビルドする

```bash:/mnt/c/Users/hogehoge/ev3rt（Windowsのユーザ名hogehogeの場合）
cd hrp2/cfg
```

```bash:/mnt/c/Users/hogehoge/ev3rt/hrp2/cfg（Windowsのユーザ名hogehogeの場合）
make
```

## 7. ソースコードをgithubから落としてmakeする
※ev3-way用のサンプルプログラムです。適宜変更してください。
[ev3way_on_off_linetrace - Github](https://github.com/korosuke613/ev3way_on_off_linetrace/tree/master)

```bash:/mnt/c/hogehoge/ev3rt/hrp2/cfg（Windowsのユーザ名hogehogeの場合）
cd ../sdk
```

```bash:/mnt/c/hogehoge/ev3rt/hrp2/sdk（Windowsのユーザ名hogehogeの場合）
git clone https://github.com/korosuke613/ev3way_on_off_linetrace.git
cd ev3way_on_off_linetrace
```

```bash:/mnt/c/hogehoge/ev3rt/hrp2/sdk/ev3way_on_off_linetrace（Windowsのユーザ名hogehogeの場合）
make app=src
```

makeが完了したら(appが生成されたら)ev3rtの開発環境構築は完了。

# EV3で動かす
1. EV3の真ん中のボタン（決定ボタン）を押してEV3RTを起動する
2. USBケーブルでEV3とパソコンをつなぐ
3. `G:¥ev3rt¥apps¥`に先ほど生成されたappを入れる(Gドライブの場合)
4. 安全な取り外しをしてからUSBケーブルを引っこ抜く
5. EV3のディスプレイに表示されている`Load App`で決定ボタンを押す
6. V3のディスプレイに表示されている`SD card`で決定ボタンを押す
7. appで決定ボタンを押す
8. 動けば成功！


<!-- 記事終わり線 -->
---

<!-- ここに脚注が来る -->
