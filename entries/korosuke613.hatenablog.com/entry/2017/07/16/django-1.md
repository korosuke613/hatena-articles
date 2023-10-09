---
Title: DjangoアプリをHerokuにデプロイする（その１）
Category:
- アーカイブ
- Python
- プログラミング
Date: 2017-07-16T00:00:00+09:00
URL: https://korosuke613.hatenablog.com/entry/2017/07/16/django-1
EditURL: https://blog.hatena.ne.jp/korosuke613/korosuke613.hatenablog.com/atom/entry/26006613632785266
---

<!-- ここに導入を書く -->
**この記事は、[https://qiita.com/Shitimi_613/items/6627d0ce042d38b86893:title]のアーカイブです。**

DjangoアプリをHeroku上で動かすときの備忘録として記録する。
その1ではDjangoアプリのスタートページ(It Workのページ)をHerokuで表示するところまでやる。

<!--  カスタムURLは`YYYY/MM/DD/name`の形式にする -->
<!-- 続きを読むのやつ -->
<!-- more -->

---

**目次**

[:contents]

---

#はじめに
DjangoアプリをHeroku上で動かすときの備忘録として記録する。
その1ではDjangoアプリのスタートページ(It Workのページ)をHerokuで表示するところまでやる。

次回 

[https://korosuke613.hatenablog.com/entry/2017/07/16/django-2:embed:cite]




**筆者の環境**

* macOS Sierra 10.12.5
* Python 3.6.1
* virtualenv 15.1.0

#必要なもの

* Herokuアカウント
参考:[Heroku初心者がHello, Herokuをしてみる](http://qiita.com/Arashi/items/b2f2e01259238235e187 "http://qiita.com/Arashi/items/b2f2e01259238235e187")

* virtualenvのインストール(python3環境にインストールされていること)
参考:[virtualenvでpython環境を管理する](http://qiita.com/caad1229/items/325ca5c8ad198b0ebce7 "http://qiita.com/caad1229/items/325ca5c8ad198b0ebce7")

#今回作るアプリの構成
今回作るアプリは以下の構成とする。

```bash:構成
myProject
├── venv
├── Procfile
├── db.sqlite3
├── manage.py
├── myDjango
│   ├──__pycache__
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
├── requirements.txt
└── runtime.txt
```
ルートディレクトリ名:`myProject`
Djangoプロジェクト名:`myDjango`


#手順
##Djangoアプリを作成する
**プロジェクトディレクトリの生成・移動**

```
$ mkdir myProject
$ cd myProject
```

**仮想環境の構築・起動**

```bash
$ virtualenv venv
$ source venv/bin/activate
```
`virtualenv venv`でvenvという名前の仮想環境を構築する。
`source venv/bin/activate`で仮想環境venvを実行する。ターミナルのユーザ名の前に`(venv)`が表示されるようになる。

(補足)
仮想環境の実行を終了したいときはターミナルで`deactivate`を実行する。

**django-toolbeltのインストール**

```bash
$ pip install django-toolbelt
```
django-toolbeltをインストールすることで、以下のパッケージがインストールされる。

* django (Django本体)
* psycopg2 (PythonでpostgreSQLを扱いやすくする)
* gunicorn (Pythonで動くWSGIサーバ)
* dj-database-url (DjangoでpostgreSQLを扱えるようにする)
* dj-static (静的リソースを扱えるようにする)

**Djangoプロジェクトの作成**

```bash
$ django-admin.py startproject myDjango ./
```
現在のディレクトリに`myDjango`というDjangoプロジェクトを作成する。

**言語・タイムゾーンを変更**
myDjangoディレクトリ内にある`settings.py`の下記の部分を書き換える。

```python
LANGUAGE_CODE = 'ja'

TIME_ZONE = 'Asia/Tokyo'
```

**マイグレーションする**

```bash
$ python manage.py migrate
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying contenttypes.0002_remove_content_type_name... OK
  Applying auth.0002_alter_permission_name_max_length... OK
  Applying auth.0003_alter_user_email_max_length... OK
  Applying auth.0004_alter_user_username_opts... OK
  Applying auth.0005_alter_user_last_login_null... OK
  Applying auth.0006_require_contenttypes_0002... OK
  Applying auth.0007_alter_validators_add_error_messages... OK
  Applying auth.0008_alter_user_username_max_length... OK
  Applying sessions.0001_initial... OK
```
こんな風になったらOK
マイグレーションは簡単に言うと、SQLを書くことなくPythonでデータベース内にテーブルを作成することができる機能である。

参考: [Rails初心者がつまずきやすい「マイグレーション」](https://www.transnet.ne.jp/2015/12/29/rails初心者がつまずきやすい「マイグレーション」colnr/ "https://www.transnet.ne.jp/2015/12/29/rails初心者がつまずきやすい「マイグレーション」colnr/")

**サーバの起動**

```bash
$ python manage.py runserver
Performing system checks...

System check identified no issues (0 silenced).
July 16, 2017 - 02:53:26
Django version 1.11.3, using settings 'myDjango.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```
`python manage.py runserver`を実行して、上のような表示になったらOK
このコマンドを実行することで、`http://127.0.0.1:8000/`でアプリを見ることができる。
終了するときは`Ctrl+C`を押す。

**ローカルでアプリが起動するか確認**
ブラウザから[http://127.0.0.1:8000/](http://127.0.0.1:8000/)にアクセスする。うまくいってれば以下の画像のようなページが表示される。

[f:id:korosuke613:20200926192008p:plain]

##gitでバージョン管理をする

**.gitignoreの作成**
.gitignoreファイルを作成し、以下を書き込む。

```planetext:.gitignore
*.pyc
venv
staticfiles
db.sqlite3
```

**コミットする**

```bash
$ git init
$ git add .
$ git commit -m "Djangoアプリを作成した"
```

##Herokuへデプロイする
**Procfileの作成**

```bash
$ echo "web: gunicorn myDjango.wsgi --log-file -" > Procfile
``` 
ProcfileはHerokuに何を実行させるか教えるためのファイル。

参考: [HerokuのProcfileの役割](http://b0npu.hatenablog.com/entry/2016/12/28/210840 "http://b0npu.hatenablog.com/entry/2016/12/28/210840")

**runtime.txtの作成**

```bash
$ echo "python-3.6.1" > runtime.txt
```
runtime.txtでPythonのバージョンを指定する。

**requirements.txtの作成**

```bash
$ pip freeze > requirements.txt
```

こういう風なファイルができる

```text:requirements.txt
dj-database-url==0.4.2
dj-static==0.0.6
Django==1.11.3
django-toolbelt==0.0.1
gunicorn==19.7.1
psycopg2==2.7.1
pytz==2017.2
static3==0.7.0
```

**DjangoアプリをHerokuで使えるようにする**

settings.pyの最後の行に以下を追加する。

```python
# Parse database configuration from $DATABASE_URL
import dj_database_url
db_from_env = dj_database_url.config()
DATABASES['default'].update(db_from_env)

# Honor the 'X-Forwarded-Proto' header for request.is_secure()
SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')

# Allow all host headers
ALLOWED_HOSTS = ['*']

# Static asset configuration
import os
BASE_DIR = os.path.dirname(os.path.abspath(__file__))
STATIC_ROOT = 'staticfiles'
STATIC_URL = '/static/'

STATICFILES_DIRS = (
    os.path.join(BASE_DIR, 'static'),
)
```

wsgi.pyを書き換える

```python
import os

from dj_static import Cling
from django.core.wsgi import get_wsgi_application

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "myDjango.settings")

application = Cling(get_wsgi_application())
```

**Herokuアプリの作成**

```bash
$ heroku create
```

**Heroku環境変数の設定**

```bash
$ heroku config:set DISABLE_COLLECTSTATIC=1
```
これをしないとpush時にエラーになる。

**Herokuにプッシュ**

```bash
$ git add .
$ git commit -m "Herokuで動くようにした"
$ git push heroku master
```
`remote: Verifying deploy... done.`と表示されたらプッシュ成功

**Herokuでもマイグレーションする**

```bash
$ heroku run python manage.py migrate
```
`heroku run 〇〇`を実行すると、heroku上で〇〇というコマンドを実行できる。

**Herokuで動いてるかを確認**

```bash
$ heroku open
```
`heroku open`で今回作ったHeroku上のアプリケーションにアクセスできる。

うまくいっていれば以下の画像のようなページが表示される。

[f:id:korosuke613:20200926192008p:plain]

#まとめ
今回はDjangoの作ったばっかのアプリをHeroku上で動かすところまでをやった。
次回は管理サイトについてとモデル作成を行う。

次回 [DjangoアプリをHerokuにデプロイする[その2]](http://qiita.com/Shitimi_613/items/60d994f0a8b9e8890d4c "http://qiita.com/Shitimi_613/items/60d994f0a8b9e8890d4c")

#参考

* [たった5分でDjangoアプリをherokuにデプロイする方法](http://qiita.com/kilhyungdoo/items/ae0b4d2970bc0937e884#gitバージョン管理を行う "http://qiita.com/kilhyungdoo/items/ae0b4d2970bc0937e884#gitバージョン管理を行う")
* [Deploying Python and Django Apps on Heroku](https://devcenter.heroku.com/articles/deploying-python "https://devcenter.heroku.com/articles/deploying-python")



<!-- 記事終わり線 -->
---

<!-- ここに脚注が来る -->
