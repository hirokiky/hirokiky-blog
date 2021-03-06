Djangoのチケット18481についての雑記
===================================

Django_ に投げていた `パッチが昨夜取り込まれた <https://code.djangoproject.com/ticket/18481#comment:11>`_ のでその話。

前に取り込まれた `チケット 18558 <http://blog.hirokiky.org/2013/02/14/added_myself_to_django_s_authors.html>`_ 以来。

チケット 18481 について
-----------------------
このチケットは、別のチケット `17277 <https://code.djangoproject.com/ticket/17277>`_ に由来してる。
17277 は POST の body 読み込み時にエラーがあれば、それ専用のエラーをあげるよう修正するというもの。

17277 以前は例外処理が入っていないので、例外があれば IOError が投げられていた。
でもこれってわかりにくいから、 IOError を継承した UnreadablePostError を投げるよう修正された。

でもこのチケット 17277 には漏れがあって、 FILES の場合はそのままだった。
なので今回取り込まれたチケット 18481 では、FILES で例外が発生するときも UnreadablePostError を
投げるよう修正しましょうというもの。

やったこと
----------
私がこのチケットをみたときには、すでにバグ修正のチケットがあったのでテストのパッチを書いた
(KyleMac に報告されて、 edevil が実装の修正を追加していた)。

owner もついていなかったしチケットも 2 ヶ月ほど放置されていたので、
ここはテストのパッチを書いてやるかと思った次第。

チケット 17277 で追加されたチケットを参考に、 FILES で例外が発生するようにしたテストを書いた。

取り込んでくれたのは claudep 、また彼にお世話になった。

.. _Django: https://djangoproject.com/

.. author:: default
.. categories:: none
.. tags:: django,misc
.. comments::
