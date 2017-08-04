django-celeryで非同期処理クイックスタートガイド
===============================================

[djangoccelery](https://pypi.python.org/pypi/django-celery)
で非同期処理をやる。

サクッと非同期処理を試せちゃうような、クイックスタートガイド
をメモがてら書いていく。

インストール
------------

まずはインストールから。

pip を使って [Django](https://pypi.python.org/pypi/Django/1.5) と
[djangoccelery](https://pypi.python.org/pypi/django-celery)
をインストールする。

``` {.sourceCode .sh}
% pip install Django
% pip install django-celery
```

おわり。

バージョンは以下のようになった

``` {.sourceCode .sh}
% pip freeze

Django==1.5
amqp==1.0.10
anyjson==0.3.3
billiard==2.7.3.23
celery==3.0.16
django-celery==3.0.11
kombu==2.5.8
python-dateutil==1.5
pytz==2013b
wsgiref==0.1.2
```

設定
----

インストールできたら [Django](https://pypi.python.org/pypi/Django/1.5)
のプロジェクト `asynctest` と、 アプリケーション `caculator`
を作ってみる。

``` {.sourceCode .sh}
% django-admin.py startproject asynktest
% cd asynktest
% python manage.py startapp caculator
```

まずは設定ファイル (asynctest/asynctest/settings.py) に
[djangoccelery](https://pypi.python.org/pypi/django-celery)
と先ほど作った `caculator` 、それとあと 裏方の `kombu.transport.django`
をいれてやる。

``` {.sourceCode .python}
INSTALLED_APPS = (
    ...
    'djcelery',
    'kombu.transport.django',
    'caculator',
)
```

こんなかんじに。

さらに、 [djangoccelery](https://pypi.python.org/pypi/django-celery)
に関する設定を追記する。

``` {.sourceCode .python}
###### django-celery configuations ######
from djcelery import setup_loader
setup_loader()
BROKER_URL = 'django://'
# Tasks will be executed asynchronously.
CELERY_ALWAYS_EAGER = False
```

あとはDBと同期すればOK。

> **note**
>
> `django-kombu` をインストールしなくても最近の `kombu` と上記の設定で
> [Django](https://pypi.python.org/pypi/Django/1.5)
> のDBをブローカーとして使えるよう。
> [djangoccelery](https://pypi.python.org/pypi/django-celery)
> インストールの段階で `kombu` もインストール されてくれるので、とくに
> `kombu` を意識する必要がなくなって しまった。

非同期処理させるもの
--------------------

さて肝心の処理をおこなう関数を書く。 caculator/tasks.py
を以下のように書いた。

``` {.sourceCode .python}
import time

from celery import task

@task
def add(a, b):
    time.sleep(10)
    return a + b
```

`task` でデコレートしてあげるだけで良い。 `add`
関数は10秒スリープしてくれるという親切設計なので、
存分に非同期を味わうことができる。

あと、モジュール名は tasks.py にしましょうね。

-   [django-celeryでモジュール名をtasks.pyにせずにハマった](http://blog.hirokiky.org/2013/03/21/use_task_py_on_celery.html)

実行してみる
------------

準備ができたので非同期を味わってみる。 まずは Celery 氏を起動。

``` {.sourceCode .sh}
% python manage.py celeryd -l info
```

別のシェルから manage.py shell を起動。

``` {.sourceCode .sh}
% python manage.py shell
```

打っていく。

``` {.sourceCode .python}
>>> from caculator.tasks import add
>>> add(1, 2) # 10秒かかる
3
>>> add.run(1, 2) # 10秒かかる
3
>>> result = add.delay(1, 2) # 非同期で実行するには delay
>>> result.ready() # ready で終了したかがわかる
False
>>> result.ready() # ﾏﾀﾞｧ?(・∀・ )っ/凵⌒☆ﾁﾝﾁﾝ
True
>>> result.get() # 終わってたら get でとる
3
>>> # 処理終わってないのに get すると、
>>> # 値が返るまで待ってしまう
>>> # タイムアウトしたい
>>> 
>>> result = add.delay(3, 4) # もっかい計算
>>> result.get(timeout=3) # 3秒間だけ待つ
Traceback (most recent call last):
  File "<console>", line 1, in <module>
  File "/path/to/celery/result.py", line 109, in get
    interval=interval)
  File "/path/to/celery/backends/base.py", line 186, in wait_for
    raise TimeoutError('The operation timed out.')
TimeoutError: The operation timed out.
>>> # 3秒以内に終わらなかったら TimeoutError
>>> result.get(timeout=3)
7
>>> # 3秒以内なら結果が返る
```

こんなかんじで使えます。 やったね。非同期処理ができたよ。

重い処理があれば非同期でやらせて、ポーリングして結果待つとか
サクッと書けてしまう。 [Django](https://pypi.python.org/pypi/Django/1.5)
内で完結してできるから分かりやすくて楽。
[djangoccelery](https://pypi.python.org/pypi/django-celery)
覚えていて損ないかと思う。

参考:

-   [Django-Celeryで非同期処理](http://hdknr.bitbucket.org/celery/)
-   [FirstStep with
    Celery](http://docs.celeryproject.org/en/latest/getting-started/first-steps-with-celery.html)
-   [Running celeryd as ad
    daemon](http://ask.github.com/celery/cookbook/daemonizing.html)

