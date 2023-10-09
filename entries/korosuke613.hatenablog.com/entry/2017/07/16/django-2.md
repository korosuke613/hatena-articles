---
Title: DjangoアプリをHerokuにデプロイする（その２）
Category:
- アーカイブ
- Python
- プログラミング
Date: 2017-07-16T00:30:00+09:00
URL: https://korosuke613.hatenablog.com/entry/2017/07/16/django-2
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613632788513
---

<!-- ここに導入を書く -->
**この記事は、[https://qiita.com/Shitimi_613/items/60d994f0a8b9e8890d4c:title]のアーカイブです。**

DjangoアプリをHeroku上で動かすときの備忘録として記録する。
その2ではDjangoアプリの管理ページを使えるようにし、モデルの作成、操作を行う。

<!--  カスタムURLは`YYYY/MM/DD/name`の形式にする -->
<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]

---


##前回やったこと
Djangoアプリのスタートページ(It Workのページ)をHerokuで表示するところまでやった。

[https://korosuke613.hatenablog.com/entry/2017/07/16/django-1:embed:cite]

##今回やること
* 管理ページを使えるようにする。
* モデルを作成し、管理ページから操作する。


**筆者の環境**

- macOS Sierra 10.12.5
- Python 3.6.1
- virtualenv 15.1.0

#必要なもの

#今回作るアプリの構成
```bash:構成
myProject
├── Procfile
├── blog
│   ├── __init__.py
│   ├── __pycache__
│   ├── admin.py
│   ├── apps.py
│   ├── migrations
│   ├── models.py
│   ├── tests.py
│   └── views.py
├── db.sqlite3
├── manage.py
├── myDjango
│   ├── __init__.py
│   ├── __pycache__
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── requirements.txt
└── runtime.txt
```

#手順
今回はタイトル、内容、日付を追加するアプリBlogを実際に作成する。


##ローカルでの作業
###新しいアプリを作成
**blogアプリを作成**

```bash
$ python manage.py startapp blog
```


**blogアプリを使えるようにする**

`myDjango/settings.py`の`INSTALLEF_APPS`にblogを追加

```python
# Application definition

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog',
]
```

###新しいモデルを作成

**blogアプリで使うモデルを作成**

`blog/models.py`に以下を追加

```python
class AddContents(models.Model):
    objects = None
    title = models.CharField(max_length=20)
    word_text = models.CharField(max_length=140)
    date_time = models.DateTimeField('投稿日')
```

**マイグレーションファイルを作成**

```bash
$ python manage.py makemigrations blog 
Migrations for 'blog':
  blog/migrations/0001_initial.py
    - Create model AddContents
```

**マイグレーションする**

```bash
$ python manage.py migrate 
Operations to perform:
  Apply all migrations: admin, auth, blog, contenttypes, sessions
Running migrations:
  Applying blog.0001_initial... OK
```


###管理ページでモデルを扱えるようにする
`blog/admin.py`を以下に書き換える。

```python
from django.contrib import admin
from .models import AddContents


class AddContentsAdmin(admin.ModelAdmin):
    list_display = ('id', 'title', 'word_text', 'date_time')


admin.site.register(AddContents, AddContentsAdmin)
```

###管理者を作成

```bash
$ python manage.py createsuperuser
Username (leave blank to use '何も入力しないとこの部分がユーザー名になる'): <ログイン時のユーザ名>
Email address: <メールアドレス>
Password: <パスワード>
Password (again): <パスワード再入力>
Superuser created successfully.
```

**サーバを起動**

```bash
$ python manage.py runserver
```

###確認
http://127.0.0.1:8000/admin にアクセス

[f:id:korosuke613:20200926192823p:plain]

ユーザ名、パスワードを入力して下のような画面になったらローカルでは成功。

[f:id:korosuke613:20200926192846p:plain]

##Heroku上で動かす
###プッシュ
```bash
$ git add .
$ git commit -m "blogアプリを作成した"
$ git push heroku master
```

###マイグレーションする
Heroku上ではマイグレーションファイルの作成は行わない(gitで管理する)。
しかし、Herokuで扱うデータベースをpostgresとしているため、Heroku上でのマイグレーション（モデルの内容をデータベースに適用）が必要となる。

```bash
$ heroku run python manage.py migrate 
```

###管理者の作成
ローカル上で作成した管理者はローカルのデータベース(SQLite3)に登録されているため、Heroku上で管理者を作成する必要がある。

```bash
$ heroku run python manage.py createsuperuser
```

###確認
**Heroku上のアプリを開く**

```bash
$ heroku open
```

**管理ページを表示する**

開かれたページのURLに`/admin`を加えて開く
例) `https://herokuアプリ名.herokuapp.com/admin`
すると[先ほど確認したようなログインページ](#)が現れるので、ログインする。

**BlogアプリのAdd contentsモデル管理ページを表示する**

サイト管理 -> BLOG -> Add contents -> 追加をクリックする。

[f:id:korosuke613:20200926192920p:plain]

**コンテンツを追加する**

[AddContentsクラス]()で設定したとおりにフィールドが表示されるので適当に入力して"保存"をクリックする。

[f:id:korosuke613:20200926192948p:plain]

**モデル管理画面で確認**

先ほど追加したコンテンツが表示されていればOK

[f:id:korosuke613:20200926193011p:plain]

#おまけ
##Djangoで扱うDBとHerokuで扱うDBについて
Djangoはデフォルトの設定では SQLiteを使用しており、簡単に使用できる。しかし、Herokuでは基本的にSQLiteが扱えず、postgreSQLが標準となっているため、[その1]()でHeroku上ではpostgreSQLを扱うように設定している。

##Heroku上のデータベースをのぞいてみる。
Heroku上のデータベースにアクセスするには以下のコマンドを実行する。

```bash
$ heroku pg:psql
```

アクセスできると、`アプリ名::DATABASE=>`と表示されるので`\z`を入力する。
注)`\z` : postgresにおいてテーブル一覧を表示する

```bash
dry-brook-87010::DATABASE=> \z
                                            Access privileges
 Schema |               Name                |   Type   | Access privileges | Column privileges | Policies
--------+-----------------------------------+----------+-------------------+-------------------+----------
 public | auth_group                        | table    |                   |                   |
 public | auth_group_id_seq                 | sequence |                   |                   |
 public | auth_group_permissions            | table    |                   |                   |
 public | auth_group_permissions_id_seq     | sequence |                   |                   |
 public | auth_permission                   | table    |                   |                   |
 public | auth_permission_id_seq            | sequence |                   |                   |
 public | auth_user                         | table    |                   |                   |
 public | auth_user_groups                  | table    |                   |                   |
 public | auth_user_groups_id_seq           | sequence |                   |                   |
 public | auth_user_id_seq                  | sequence |                   |                   |
 public | auth_user_user_permissions        | table    |                   |                   |
 public | auth_user_user_permissions_id_seq | sequence |                   |                   |
 public | blog_addcontents                  | table    |                   |                   |
 public | blog_addcontents_id_seq           | sequence |                   |                   |
 public | django_admin_log                  | table    |                   |                   |
 public | django_admin_log_id_seq           | sequence |                   |                   |
 public | django_content_type               | table    |                   |                   |
 public | django_content_type_id_seq        | sequence |                   |                   |
 public | django_migrations                 | table    |                   |                   |
 public | django_migrations_id_seq          | sequence |                   |                   |
 public | django_session                    | table    |                   |                   |
(21 rows)
```

実際にSQL文を入力するとデータベースを操作できる。

```bash
dry-brook-87010::DATABASE=> select * from blog_addcontents;
 id |   title    |       word_text        |       date_time
----+------------+------------------------+------------------------
  1 | こんにちは   | 今日もいい天気でした。     | 2017-07-15 19:28:33+00
(1 row)
```

#まとめ
今回は管理ページを使えるようにし、新しいアプリとそのモデルを作成し、Heroku上でデータベースを管理するところまでをやった。
次回はテンプレートを作成し、今回作ったブログを外部に公開できるところまでをする。(予定)

#参考
http://tech.pjin.jp/blog/2017/06/29/python-primer-25-django-11/



<!-- 記事終わり線 -->
---

<!-- ここに脚注が来る -->
