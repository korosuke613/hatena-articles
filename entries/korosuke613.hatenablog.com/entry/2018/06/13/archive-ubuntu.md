---
Title: archive.ubuntu.comにつながらない...
Category:
- アーカイブ
- Unix/Linux
- ノウハウ
Date: 2018-06-13T00:00:00+09:00
URL: https://korosuke613.hatenablog.com/entry/2018/06/13/archive-ubuntu
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613632488962
---

<!-- ここに導入を書く -->
**この記事は、[https://midorigame201845.hateblo.jp/entry/2018/06/13/003238:title]のアーカイブです。**

ubuntuで`apt-get install`していると以下のようなエラーが出た。どうやらページにアクセスできないらしい。

<!--  カスタムURLは`YYYY/MM/DD/name`の形式にする -->
<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]

---
# Undetermined Error
ubuntuで`apt-get install`していると以下のようなエラーが出た。どうやらページにアクセスできないらしい。

```
Failed to fetch http://archive.ubuntu.com/ubuntu/pool/main/g/gcc-7/libasan4_7.3.0-16ubuntu3_amd64.deb  Undetermined Error
```

# ブラウザで見てみる

アクセスしてみると、確かにつながらない。

[f:id:korosuke613:20200926000518p:plain]

ていうかそもそも[Archives Ubuntu](http://archive.ubuntu.com/
)につながらない。

> *2018/07/02*
> *リンクが間違っていたので訂正しました。*

[f:id:korosuke613:20200926000614p:plain]

# DNSサーバを変更してみる
家のネットのプロバイダはbb.exciteであり、DNSサーバも恐らくそこのモノにしているのだが、試しにDNSサーバをgoogleの8.8.8.8にしてみた。

[f:id:korosuke613:20200926000637p:plain]

# 上記のapt-getのエラーは出なくなったが、やっぱりブラウザではつながらない

どういうことか、`apt-get install`はうまくできたのに、相変わらずブラウザではつながらない。

[f:id:korosuke613:20200926000614p:plain]

なぜだ。

# 結論：セキュリティソフトが悪さをしていました。

私はESET ENDPOINT ANTIVIRUSというセキュリティソフトを使っているのだが、このソフトの**Webアクセス保護**という機能が、[Archives Ubuntu](archive.ubuntu.com/
)へのアクセスをブロックしていた。

とりあえず一時的に機能を無効にしてみる。
[f:id:korosuke613:20200926000750p:plain]

つながった。
[f:id:korosuke613:20200926000802p:plain]


<!-- 記事終わり線 -->
---

<!-- ここに脚注が来る -->
