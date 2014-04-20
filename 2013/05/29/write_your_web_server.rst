PythonでWebサーバー書きましょう
===============================

「Webサーバー書いたことある？」

と聞かれ、何じゃらホイと尋ねると

「書いたことあれば分かりそうな問題にハマってる事例見てね。みんな書いたことあるわけじゃないのね、と思って」

とのこと。

そういえば私も書いたこと無かったのでWebの悟りを開くために書いた。

書いた
------
- garam.py: https://gist.github.com/hirokiky/5669798

リクエストからURIとって、静的ファイルを読み込んでそのまま返す荒々しいヤツ

- 404は返せません。
- 500も返せません。
- /etc/passwdは返せます。

`socket-低レベルネットワークインターフェース <http://docs.python.jp/2.7/library/socket.html>`_
読んだだけ。

周りの人の話
------------
- とりあえずsocketで通信多重度1のstatic file返すだけのサーバー書くだけでもかなり勉強になると思うよ
- `waitress <https://github.com/Pylons/waitress>`_ 読みやすくていいよ
- GETだけなら簡単だよ
- python以外で勉強として作りたいなら C でソケット開いて、まじめにパースする

との教えが。

まとめ
------
自己同一性の達成過程においてWebサーバー書くことは重要らしい。

ふとWSGIサーバーとか書きたくなる。

.. author:: default
.. categories:: none
.. tags:: python,
.. comments::
