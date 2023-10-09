---
Title: PostgreSQLコマンドチートシート
Category:
- アーカイブ
Date: 2016-07-01T00:00:00+09:00
URL: https://korosuke613.hatenablog.com/entry/2016/07/01/postgres-command
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613632466949
---

<!-- ここに導入を書く -->

**この記事は、[https://qiita.com/Shitimi_613/items/bcd6a7f4134e6a8f0621:title]のアーカイブです。**


よく使うPostgreSQLで利用可能なコマンドのチートシートです。

環境：psql (PostgreSQL) 9.5.0

<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]

<!-- ここに広告が入る -->
---

##端末上で使うコマンド
####サーバの起動
`$ pg_ctl start -D /usr/local/var/postgres`

####サーバの終了
`$ pg_ctl stop -D /usr/local/var/postgres`

####サーバが起動しているかの確認
`$ ps aux | grep postgres`

####データベース接続
`$ psql -d database -U user -h host`

* -d: データベース名(未指定だと、ログインユーザー名のデータベースに接続する)
* -U: ユーザ名(未指定だと、ログインユーザー名になる)
* -h: ホスト名(未指定だと、localhostになる)

####データベース一覧表示
`$ psql -l`

####PostgreSqlバージョン表示
`$ psql -V`

####PostgreSqlに関するヘルプ
`$ psql -help`


##psql上で使うコマンド
postgresの部分には接続中のDB名が入る。

####psqlの終了
`postgres=# \q`

####ユーザ一覧を表示
`postgres=# \du`

####データベース一覧を表示
`postgres=# \l`

####他のデータベースに接続
`postgres=# \c dbname`

####データベース作成
`postgres=# create database dbname;`

####接続中のデータベースの情報を表示
`postgres=# \conninfo`

####テーブル一覧を表示
`postgres=# \z`

####テーブル定義を確認
`postgres=# \d tablename`

*tablename*には任意のテーブル名を入れる。

####カレントディレクトリ変更
`postgres=# \cd directory`

カレントディレクトリをdirectoryに変更する。

####CSV形式のファイルをテーブルに挿入
`postgres=# \copy tablename from filename DELIMITER AS ','`

####ファイルからコマンドを実行
`postgres=# \i filename.sql`

ファイルから入力を読み取り、実行する。

####コマンドラインの履歴の表示
`postgres=# \s`

\sの後にファイル名を入力すると、そのファイル名に結果を出力する。

####'\'に関するヘルプの表示
`postgres=# \?`

####シェル上のコマンドを使いたい場合
`postgres=# \! command`

*command*の部分にlsやpwdを入れるとpsql上でもシェル上のコマンドが実行できる。



##参考
* [pg_ctl - PostgreSQL 9.2.4文書](https://www.postgresql.jp/document/9.2/html/app-pg-ctl.html)
* [psql - PostgreSQL 9.2.4文書](https://www.postgresql.jp/document/9.2/html/app-psql.html)
* [PostgreSql コマンドの覚え書き](http://qiita.com/mm36/items/1801573a478cb2865242)

こちらのサイトを参考に書かせていただきました。ありがとうございましたm(_ _)m

<!-- 記事終わり線 -->
---

<!-- ここに脚注が来る -->
