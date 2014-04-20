django-celeryでモジュール名をtasks.pyにせずにハマった
=====================================================

django-celery_ でハマった。
django-celery_ の場合、 `manage.py celeryd` を読んでやるだけで、
とくにモジュールの指定や celery インスタンスの生成が要らない。

そういった設定は setting.py に書いておいてやってあとは勝手にやってくれる。
だけど、今回はこれにハマった。

非同期処理を行わせる処理はすべて `tasks.py` というモジュールにいれてやる
必要がある::

    PROJECT_NAME/APP_NAME/tasks.py

これを知らずに runner.py だか workflow.py だかにいれてやっててエラーになっていた。
状況としては以下の様な関数を `tasks.py` **以外の** モジュールに書いていた。

.. code-block:: python

    @task(name='test')
    def hoge():
        return 'hoge'

これで

.. code-block:: python

    >>> from hogeapp.hoge import hoge
    >>> hoge.delay()

実行。

そうすると Celery 氏が泣く。

.. code-block:: sh

    [2013-03-21 23:04:47,126: INFO/MainProcess] consumer: Connected to django://localhost//.
    [2013-03-21 23:05:12,227: ERROR/MainProcess] Received unregistered task of type 'test'.
    The message has been ignored and discarded.
    
    Did you remember to import the module containing this task?
    Or maybe you are using relative imports?
    More: http://docs.celeryq.org/en/latest/userguide/tasks.html#names
    
    The full contents of the message body was:
    {'utc': True, 'chord': None, 'args': [], 'retries': 0, 'expires': None, 'task': 'test', 'callbacks': None, 'errbacks': None, 'taskset': None, 'kwargs': {}, 'eta': None, 'id': '5d887f1a-e72c-46d2-8f64-16da02cf6780'} (184b)
    
    Traceback (most recent call last):
      File "/home/hirokiky/dev/hogehogeenv/local/lib/python2.7/site-packages/celery/worker/consumer.py", line 592, in receive_message
        self.strategies[name](message, body, message.ack_log_error)
    KeyError: 'test'

`tasks.py` に入れておいてやらないと、読み込んで登録しておいてくれないっぽい。

ためしに ``worker/consumer.py`` の592行目に潜り込んでみたら、たしかに `self.strategies`
には無かった。

ただ手元のシェルで見てみると登録されているように見えたので、
「ちゃんと登録されてるしー」と思っていたのが罠。

.. code-block:: python

    >>> from celery import registry
    >>> registry.tasks
    {'celery.chain': <@task: celery.chain>, ... 'hoge': <@task: hoge>}

あると思ったけど、結局はCeleryd側では読めてないっぽかった。

これの解答になりそうなものはあった。

- http://stackoverflow.com/questions/9769496/celery-received-unregistered-task-of-type-run-example

でも ``celery.registry.TaskRegistry`` なんてクラスなかったのでスルーした。

まぁ、ドキュメントちゃんと読んだら書いてあったので、解決::

    Tasks are defined by wrapping functions in the @task decorator. 
    It is a common practice to put these in their own module named tasks.py, 
    and the worker will automatically go through the apps in INSTALLED_APPS
    to import these modules.

http://docs.celeryproject.org/en/latest/django/first-steps-with-django.html

何かにつけて思うけど、ドキュメントまじワイフ。

.. _django-celery: https://pypi.python.org/pypi/django-celery

.. author:: default
.. categories:: none
.. tags:: django,celery,memo
.. comments::
