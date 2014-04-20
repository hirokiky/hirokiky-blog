Pushosuというサービスをリリースしました
=======================================
Twitter投稿用のボタンを自分で作れるサービス、 `Pushosu <http://pushosu.hirokiky.org/>`_ を作りました

何よこれ
--------
Twitter投稿用のボタンを作れるサイトです。

1) Twitterからログインして、ボタンを作ります
2) 作ったボタンをクリックします
3) ボタンの内容でツイートされます

気が向いたときに作ったボタンを押せば、いつでも同じ内容でツイートできるというわけですね

思いついたわけ
~~~~~~~~~~~~~~
毎日のようにRedbullを飲んでいて、その度に:

- *Redbull gokgok!*

とツイートするのは面倒くさいです。YoruFukurouに毎回手で打ち込んであげなきゃいけない。じゃぁ作るかと。
`ui_nyanめ... <http://me.uinyan.com/>`_ 面白い。

Pushosuの実装
-------------
Python製Webフレームワーク `Pyramid <http://www.pylonsproject.org/projects/pyramid/about>`_ を主にしています。

- `Pyramid <http://www.pylonsproject.org/projects/pyramid/about>`_
- `peewee <https://github.com/coleifer/peewee>`_
- `WTForms <http://wtforms.simplecodes.com/docs/1.0.2/>`_
- `Jinja2 <http://jinja.pocoo.org/docs/>`_

あたりでできています。関連して

- `pyramid_peewee <https://bitbucket.org/podhmo/pyramid_peewee>`_
- `pyramid_jinja2 <http://docs.pylonsproject.org/projects/pyramid_jinja2/en/latest/>`_

にお世話になりました。あとは

- MySQL
- Apache(mod_wsgi)

など。

動機
----
`Pyramid <http://www.pylonsproject.org/projects/pyramid/about>`_ というWebフレームワークを触ってみたかったのが動機です。
(「作りたいもの」が思いついたからではないです。そういうのは常にあります)

何かやるなら作ってみないと覚えられない体質なので、作りました。

Pyramidに興味持ったきっかけ
~~~~~~~~~~~~~~~~~~~~~~~~~~~
`@podhmo <http://twitter.com/podhmo/>`_ さんの記事 `2012 Pythonアドベントカレンダー 19日目 pyramidでseparation <http://pod.hatenablog.com/entry/2012/12/19/225042>`_ を読んで

**「Σヽ(｀д´；)ﾉ うおおおお！view_configやべええええええええ」**

ってなったのでPyramidに興味持ちました。(参考: `Pythonアドベントカレンダー2012 <http://connpass.com/event/1439/>`_)

以前に `Django & Pyramid Con JP 2012 <http://djangoproject.jp/weblog/2012/09/17/django-pyramid-con-jp-2012-finished/>`_ というカンファレンスを開催したときからPyramidにはフツフツと興味があったので、まぁいい機会だし(年末休みもあるし)触ってみようかなと。

さわれるPyramid
~~~~~~~~~~~~~~~
私は普段「Django!Django!(ﾟ∀ﾟ)o彡゜」って言っていますが(`Django <http://djangoproject.jp/>`_ もPython製Webフレームワークです) 、Pyramidも素晴らしいものでした。

Pyramidはよく「難しい」と言われているような気がします。ですが、そうではないです。
処理の流れが見えやすいですし、Viewも簡潔かつ直感的に書けますし(view_config, custom_predicateなど)。
Webアプリケーションを作っているだけでも流れがシックリくると思います。

ただし、Pyramidと付随してよく利用されるSQLAlchemy、makoは考えること、手順、慣れがそれなりに必要だと感じました。
代わりにpeewee、Jinja2を使ってみると(Django大好きな私にはとくに)わかりやすくサクサクと作れました。

このようにコンポーネントを選択できることや、Pyramid自体がさまざまなPythonの資産を活用していることなど、よくできてるなぁという印象です。
フレームワークは適材適所使えばいいですが、Pyramidはその幅が調整しやすそうで良い感じです。

(；´Д｀)ｽｯ､ｽﾊﾞﾗｽｨことに日本語ドキュメントありますので、ぜひ年末年始にPyramidで何か作ってみましょう

- `Pyramid Web アプリケーション開発フレームワーク <http://docs.pylonsproject.jp/projects/pyramid-doc-ja/en/latest/index.html>`_

おわりに
--------
- `Pushosu <http://pushosu.hirokiky.org/>`_ 作ったよ
- `Pyramid <http://www.pylonsproject.org/projects/pyramid/about>`_ 難しくないよ
- `Django <http://djangoproject.jp/>`_ はやっぱり素敵だよ (`Django勉強会 <http://connpass.com/event/1566/>`_ やるよ)

.. author:: default
.. categories:: none
.. tags:: pushosu,release,pyramid
.. comments::
