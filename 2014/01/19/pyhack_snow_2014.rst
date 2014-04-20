Python mini Hack-a-thon 雪山合宿 2014 に行ってきた
=======================================================

`Python mini Hack-a-thon 雪山合宿 2014 <http://connpass.com/event/3703/>`_

今回3回目の参加になる､Python mini hack-a-thonの合宿版です｡
スキーはせずに引きこもって開発してました｡

開発したもの:

* genaa_ 0.4 リリース
* invoke_ にプルリクエストを送った

genaa 0.4リリース
--------------------

genaa_ というASCII Art生成ツールの最新版です｡
日本語など､1文字で2幅以上持つ文字列に対応しました｡
:tw:`@nakamuray` さんがフォークして開発しててくれたので取り込ませてもらいました｡

invokeにプルリク
------------------

invoke_ はローカルでのコマンドをまとめるようなツールで､Pythonで書けるmakeみたいなものです｡
実行できるタスクの一覧を 'invoke --list' で取れるんですが､
その際に各タスクの説明のサマリを表示するようにする対応をしていました｡
まだ反応がないのでどうなるかわかんないですが｡

* https://github.com/pyinvoke/invoke/pull/110


遊んだテーブルゲーム
-----------------------

* `Love Letter <http://www.tk-game-diary.net/love_letter/love_letter.html>`_: さっくり楽しめる&導入も簡単な割によくできてる印象｡
* `犯人は踊る <http://bodogegiga.jugem.jp/?eid=42>`_: かなり面白かった｡犯人は誰なのかと推理していく感じがよかった｡
* `Dungean of Mandom <http://gioco.sytes.net/dungeon_of_mandom.htm>`_: あんまり掴めなかった｡

提供は :tw:`@kiris` さんより｡

.. _genaa: https://pypi.python.org/pypi/genaa/
.. _invoke: https://github.com/pyinvoke/invoke/

.. author:: default
.. categories:: none
.. tags:: pyhack
.. comments::
