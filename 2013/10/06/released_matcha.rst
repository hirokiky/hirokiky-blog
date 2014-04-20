matcha0.1リリースのお知らせとその背景
=======================================

matcha という WSGI dispatcher をリリースしました｡
WSGIのライブラリ/ミドルウェアで､PATH_INFOを考慮した
WSGIアプリケーションの呼び出しを主目的にしています｡

* `matcha (PyPI) <https://pypi.python.org/pypi/matcha>`_

この記事では matcha_ の紹介を軽くしつつ､
後半では本音として書きたかった dispatcher を実装して思ったことを書きます｡

matchaの利点
------------
matcha_ が売りにできるのは **Djangoのurls.pyっぽく書ける** の､1点ですね｡
基本的にはこんなかんじで記述します:

.. code-block:: python

    >>> from matcha import Matching as m, bundle
    >>> 
    >>> from yourproject import home_app
    >>> from yourproject.blog import post_list_app, post_detail_app
    >>> 
    >>> matching = bundle(
    ...     m('/', home_app, 'home'),
    ...     m('/post/', post_list_app, 'post_list'),
    ...     m('/post/{post_slug}/', post_detail_app, 'post_detail'),
    ... )

bundleとかMatchingとかちょっと名前が違うくらいですね｡

他にも matcha_ は以下の点において便利です:

* 上記のように宣言的に書けるし､手続き的にも書ける
* 名前(上記の例だと'post_listなど')からURLの逆引きができる
* WSGIアプリケーションの呼び出し意外にも使える

機能的に matcha_ は他のdispatcherと大して変わらないです｡
他にもいくつか dispatcher ありますが､ matcha_ を除いた中では
WebDispatchが良さげです:

* `WebDispatch <https://github.com/aodag/WebDispatch>`_
* `urlrelay <https://bitbucket.org/lcrees/urlrelay/overview>`_
* `gargant.dispach <https://github.com/hirokiky/gargant.dispatch>`_

dispatcherを4つくらい作って思った
-----------------------------------
公開したもの2つ､公開してないもので2､3 dispatcher を書いてみた印象としては
**URL での dispatch は分割して用意したほうが良い** ということです｡

URL での dispatch にはある程度持っておきたい機能があります:

* environのPATH_INFO, SCRIPT_NAMEに副作用を起こす
* URLからの引数取得
* 名前/対象アプリケーションから URL の逆引き

これらの機能を実現しつつ､「URL にとらわれない柔軟な dispatcher」を作るのは
難しかったです(詳細は後述)｡

matcha_ では(少なくとも 0.1 リリースでは) URL からのマッチングのみ
提供し､それ意外の条件での dispatch は利用者や matcha_ を使ったWebフレームワーク
の開発者に提供してもらうことにします｡

route_nameを噛ませればPyramidのように使えますし､URLでのdispatchしか提供しないので
あればDjangoのようになります｡

matchaと私とときどきgargant.dispatch
---------------------------------------
つい1ヶ月ほど前に
`gargant.dispach <https://github.com/hirokiky/gargant.dispatch>`_ を
リリースし､ `PyCon APAC 2013 <http://apac-2013.pycon.jp/>`_ のLTでも紹介したばかり
ですが､ matcha_ を書き始めました｡その経緯など｡

gargant.dispatch はかなり優秀で実装も面白いのですが､柔軟にしすぎようとしたせいもあり
以下の点で URL dispatcher として劣っていました｡:

* PATH_INFO, SCRIPT_NAMEに副作用を起こさない
* 名前/対象アプリケーションから URL の逆引きができない
* URLから引数を取るのが面倒

先ほども少し触れたところです｡

これは gargant.dispatch が「matchingというものを PATH_INFO や REQUEST_METHODのみを
対象にしない」という考えのもとに作られているので「URLの逆引き」のような PATH_INFO に必ず
依存する実装が持たせにくいというものでした｡

もちろんやりようによってはできると思いますが､柔軟性を維持しつつ
そういった制約(WSGIの仕様やWebアプリケーションでよく使われる機能)への対応
を入れるのは意外と難しかった(だるかった)です｡

まぁ gargant.dispatch もかなり良い勉強になったのでこのまま廃れてもまぁいいかなという思いです｡
パッケージにおける `gargant` 以下は実験的なWebフレームワークを作るための場所としていますし｡
ただ実験的なものだけじゃなくて､実用的なものもちゃんと作っておきたいところなので､ matcha_
は集大成でもありますね｡

テンプレートエンジンでも作るか
------------------------------
と思っています｡
そもそも私は別に「dispatcherを書きたいオジサン」というわけでなく､
Webアプリケーションにおいてサーバーサイドで必要なものを下から見た場合に
dispatcherがあったということです｡すでにWebサーバーは書き散らして飽きて､
Request/Responseオブジェクトはだるかったので飽きてます｡

なので次はテンプレートエンジンでも作ってみようかなと思っています｡

.. _matcha: https://github.com/hirokiky/matcha

.. author:: default
.. categories:: none
.. tags:: matcha,wsgi,release
.. comments::
