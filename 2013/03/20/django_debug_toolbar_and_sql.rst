DjangoのORMが実行してるSQLの見方 (とdjango-debug-toolbar のススメ)
=====================================================================
django-debug-toolbar_ 使おう。

以下の様な記事があった。

- `Djangoの発行する生SQLが見たい <http://kk6.hateblo.jp/entry/2012/08/01/Django%E3%81%AE%E7%99%BA%E8%A1%8C%E3%81%99%E3%82%8B%E7%94%9FSQL%E3%81%8C%E8%A6%8B%E3%81%9F%E3%81%84>`_
- `ForeignKeyとfilterのメモ - AtAsAtAmAtArA <http://atasatamatara.hatenablog.jp/entry/20120730/1343650783>`_

`django.db.connection.queries` を使うとよいそうです。でもこれを覚えるのはちょっと大変。

ここで django-debug-toolbar_ 使えばもっと楽にできるのでオススメしたい。
どうするのかというと、 django-debug-toolbar_ の `debugsqlshell` という機能を使う。
これは普段の `manage.py shell` とほとんど同じなんだけど、SQLが実行されたときは、そのSQLを表示してくれるというもの。

(django-debug-toolbar_ は画面上にデバッグ用のパネルを表示してくれたりする、頼もしいやつ)

django-debug-toolbar_ をインストールすると管理コマンドで `debugsqlshell` というのが
使えるようになるので、設定とかもそれほど必要ない。

インストール:

.. code-block:: sh

    $ easy_install django-debug-toolbar

設置ファイルに記述:

.. code-block:: python

    # settings.py
    INSTALLED_APPS += ('debug_toolbar',)

ってすれば、もう使える。
django-debug-toolbar_ の機能をモリモリ使うなら他にも設定は要るけど、
`debugsqlshell` だけなら、まぁこれでもOK。

.. code-block:: python

    # $ ./manage.py debugsqlshell

    >>> from django.contrib.auth.models import User
    >>> User.objects.all()
    SELECT "auth_user"."id",
           "auth_user"."username",
           "auth_user"."first_name",
           "auth_user"."last_name",
           "auth_user"."email",
           "auth_user"."password",
           "auth_user"."is_staff",
           "auth_user"."is_active",
           "auth_user"."is_superuser",
           "auth_user"."last_login",
           "auth_user"."date_joined"
    FROM "auth_user" LIMIT 21  [0.40ms]
    
    [<User: hirokiky>]
    >>> qs = User.objects.all()[:10]
    >>> qs
    SELECT "auth_user"."id",
           "auth_user"."username",
           "auth_user"."first_name",
           "auth_user"."last_name",
           "auth_user"."email",
           "auth_user"."password",
           "auth_user"."is_staff",
           "auth_user"."is_active",
           "auth_user"."is_superuser",
           "auth_user"."last_login",
           "auth_user"."date_joined"
    FROM "auth_user" LIMIT 10  [0.35ms]
    
    [<User: hirokiky>]
    >>> 

こんなかんじ。
クエリセットが評価されてSQLが投げなれるタイミングもよくわかる。

でも django-debug-toolbar_ の本領はこんなもんじゃなくて、画面上にデバッグ用のパネルを表示してなんぼのもの。
使うデバッグ用のパネルは設定で選べるんだけど、とくに良いのが `debug_toolbar.panels.sql.SQLDebugPanel` 。
これを追加してあげれば、1リクエスト中に走ったSQLと、その実行時間をガントチャートで表示してくれる。
やたら遅い画面とかあるときに原因 (クエリ1000件以上投げてるじゃん!とか) をすぐ発見できるのでオススメ。

django-debug-toolbar_ がいないと生きていけない。

.. _django-debug-toolbar: https://pypi.python.org/pypi/django-debug-toolbar

.. author:: default
.. categories:: none
.. tags:: django,django-debug-toolbar,orm
.. comments::
