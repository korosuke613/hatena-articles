---
Title: 【BoW】ev3rt開発環境をWindowsで作る1
Category:
- ETロボコン
- アーカイブ
Date: 2017-10-15T00:00:00+09:00
URL: https://korosuke613.hatenablog.com/entry/2017/10/15/ev3rt-bow-1
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613632790783
---

<!-- ここに導入を書く -->
**この記事は、[https://qiita.com/Shitimi_613/items/09bec911048e55285868:title]のアーカイブです。**

<!--  カスタムURLは`YYYY/MM/DD/name`の形式にする -->
<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]

---

# はじめに

今回はWindowsでBashを使えるようにします。

**[2018/01/17 追記]
[Windows10 HomeでDockerを使って簡単にTOPPERS/EV3RTのビルド環境を構築する](https://qiita.com/Shitimi_613/items/35b3a30ba55ee6f8e01d)を公開しました。本稿の方法よりも簡単にビルド環境を構築できます。**

## 対象
OS: Windows10 バージョン1703以上

※1703からUbuntuのバージョンが16.04になる為。それより前のバージョンでは`make`時にエラー(`mno-pic-data-is-relative`)が出ます。

[[参考] Windows10 Creaters Updateへの更新](https://support.microsoft.com/ja-jp/help/4028685/windows-get-the-windows-10-creators-update)

## Bash on Ubuntu on Windowsについて
Windows上で動くUbuntuの上で動くBashのことです。
今回行うEV3のプログラミングはWindowsのままではできないのでBash on Ubuntu on Windowsを導入します。

[f:id:korosuke613:20200926193601p:plain]

**参考**
[Bash on Ubuntu on Windowsとは？ そのインストールと使い方](http://www.buildinsider.net/enterprise/bashonwindows/01)


# 環境構築

## 0. Windowsのバージョン確認
1. 「スタート」を右クリック
2. 「システム」をクリック
3. 「Windowsの仕様」の、「バージョン」の右の数字を確認する。

* `1703`以上の数字であれば「1. Bashの有効化」に進む。
* `1703`未満の数字であれば[Windows10 Creaters Updateを適用しておく](https://support.microsoft.com/ja-jp/help/4028685/windows-get-the-windows-10-creators-update)

[f:id:korosuke613:20200926193623p:plain]


## 1. Bashの有効化
1. 「スタート」で右クリック
2. 「アプリと機能」をクリック
3. 「関連設定」の「プログラムと機能」をクリック
4. 「Windowsの機能の有効化または無効化」をクリック
5. 「Windows Subsystem for Linux (Beta)」にチェックして「OK」をクリック（インストールが始まる）
[f:id:korosuke613:20200926193642p:plain]
6. インストール後に再起動する

## 2. 開発者機能の有効化
1. 「スタート」をクリック
2. 「設定」をクリック
3. 「更新とセキュリティ」をクリック
4. 「開発者向け」をクリック
5. 「開発者モード」にチェックを入れる

## 3. Bashのインストール
1. 「Windows PowerShell(管理者)」を開く
2. `bash`と入力してENTER
3. YesかNoか尋ねられるので、「y」と入力し、ENTER
4. しばらくまつ
[f:id:korosuke613:20200926193702j:plain]
5. 上の画像の状態になったら「y」と入力し、ENTER
6. 画面の指示に従い、ユーザ名・パスワードを設定する(Windowsのものと同じにする必要はない)
7. 「Windows PowerShell(管理者)」を閉じる

**Bashの開き方**

1. 「スタート」をクリック
2. 「ubuntu」と入力
3. 「Bash on Ubuntu on Windows」をクリック

## 4. リポジトリの変更

[【新】Bash on Ubuntu on WindowsでWindows上にLinux環境を導入する](http://ikesan009.sakura.ne.jp/2017/07/11/【新】bash-on-ubuntu-on-windowsでwindows上にlinux環境を導入する/)
> 標準設定ではパッケージ取得の時にいちいち海外サーバーにアクセスしてしまうため非常に効率が悪く、時間がかかってしまいます。なので、リポジトリの取得先を日本サーバーに変更します。先程と同様に次のコマンドを入力します。

```bash
sudo sed -i -e 's%http://.*.ubuntu.com%http://ftp.jaist.ac.jp/pub/Linux%g' /etc/apt/sources.list
```

上記のスクリプトをBashに入力する。

## 5. パッケージを最新版にする

```bash
sudo apt-get update
```

```bash
sudo apt-get upgrade -y
```

## 6. ログイン時のディレクトリを変更
*hogehoge***に当たる部分をWindowsのユーザ名に書き換えてください**
もし、Windowsのユーザ名が日本語の場合、不具合が起きる可能性があるので、半角英数字のユーザ名にしましょう

```bash:ユーザ名がhogehogeの場合
sudo echo "cd /mnt/c/Users/hogehoge" >> ~/.bashrc
```

# 続く

[https://korosuke613.hatenablog.com/entry/2017/10/15/ev3rt-bow-2:embed:cite]

TOPPERS/EV3RTの環境構築をします。


<!-- 記事終わり線 -->
---

<!-- ここに脚注が来る -->
