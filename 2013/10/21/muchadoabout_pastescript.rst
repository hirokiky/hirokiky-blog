===================
 PasteScript空騒ぎ
===================
PasteScript_ の話｡

PasteScriptって?
================

アプリケーションに必要なコマンドを作る地盤を提供してくれるものです｡
PasteScript_ 自身が提供するものでは､テンプレートからアプリケーションを作成する
コマンドがあります:

.. code-block:: sh

    paster create

テンプレートを作っておけばアプリケーションがさくっとできます｡

この説明だけだとなんじゃそりゃという感じですが､ここで PasteDeploy_ も紹介します｡

PasteDeploy_ はWSGIアプリケーションの構成を設定ファイルから指定できるものです｡
例えばDBの設定やLoggingの設定をファイルに記述して､その設定を元に
アプリケーションを構築､起動できます｡

この PasteDeploy_ と PasteScript_ を併用することで以下のようなコマンドで
簡単にWSGIアプリケーションが起動できたりします:

.. code-block:: sh

    paster serve

この serve コマンドも PasteScript_ の提供するもので､ PasteDeploy_ の
機能を使って簡単にWSGIアプリケーションを構築､起動できます｡
(必要な設定はdevelopment.iniのようなiniファイルに書いておきます)

WSGIアプリケーションで開発するにも Django でいう management command とか
startapp が欲しくなりますが､まぁ PasteScript_ はそんなとこです｡

PasteScript空騒ぎ
=================

その PasteScript_ ､WSGIアプリケーションやWebフレームワークで広く使われており
デファクトスタンダードといっても良いくらい有用なものですが､一つ大きな問題がありました｡

Python3 に対応していないということです｡(あぁレガシー)｡

で一番困るのは PasteScript_ を使っていた各Webフレームワークたちです｡
Webフレームワークも「よしPython3に対応するか」と思うわけですが､
依存するパッケージがPython3に対応していないと話になりません｡

ここで各フレームワークが頑張って脱 PasteScript_ を模索します｡

Pyramid はその内部に PasteScript_ を取り込み/修正し､ pcreate などの
コマンドを提供しました(Python3対応をしている PasteDeploy_ はPyramid1.4の現在でも使われています)

Turbogears は gearbox_ を用意しました｡これは PasteScript_ の代替になる
パッケージで､ Python3 でも動作します｡

いっぽうDjangoは PasteScript_ に依存せず独自のmanagement commandという地盤と
project/application templateのしくみを提供していたので､全く関係ありませんでした｡

gearboxおすすめ
===============
さてさて PasteScript_ が使えない今､WSGIアプリケーションを作る際には
どうしたものでしょうか｡

gearbox_ を使いましょう｡

これはTurboGears2がPython3対応する際にできたパッケージですが､TurboGearsに無関係な
WSGIアプリケーションでも使えます｡ついでに PasteDeploy_ に依存して serve 用の
コマンドも直接提供してくれます:

.. code-block:: sh

    gearbox serve

gearbox_ はどのWSGIアプリケーションでも使えて素晴らしいです｡
`WeiWei <https://github.com/hirokiky/weiwei>`_ というWebフレームワークレス
なWSGIアプリケーションを最近書いたのですが､これでも使わせてもらってます｡

ついでにScirptとかDeployとかややこしいのをマルっと一つにしてるあたりも分かりやすいです｡

gearbox_ 素晴らしい｡WSGIアプリケーション+Python3を書くならこれを使うのを薦めます｡

PasteScriptのPython3対応
========================

さてPython3非対応でお騒がせした PasteScript_ ですが､
つい最近今年の 9 月にようやく Python3 に対応したようです!!

すごい!!やった!!!!

と思いきや､残念なことに 2to3 を前提としています｡

* https://bitbucket.org/ianb/pastescript/pull-request/5/python-3-support/activity

まぁこれは使いたく無いですね｡
classifierも書き換えられてないしインストール時に2to3がかかるようにもしてないようです｡
(gearbox_ は現時点でまだ0.0.2ですが Python3 でも使えるいいこですよ٩(๑❛ᴗ❛๑)۶)

このマージを見つけたときは興奮しましたがちょっと違う｡
PasteScript_ をPython3対応をちゃんとさせたいんだという人は
aodagさんのプルリクエストを修正/レビュー/応援すると良いと思います｡

* https://bitbucket.org/ianb/pastescript/pull-request/6/support-py3/diff

私は gearbox_ の恩恵に甘えます｡

まとめ
======

gearbox_ 使いましょう｡

余談
====

世の中に入門記事ばっかり溢れてもつまらないので､ちょっとした小ネタを書きました｡
`パッケージの歴史の話 <https://gist.github.com/knzm/5185369>`_ とかも結構面白い
ので､こういうどうでもよくないどうでもいい記事が増えればいいと思います｡

(PasteScript_ や Pyramid のPy3対応については人から聞いた話も多いので
間違ってたら教えてください｡たまに PasteDeploy_ と逆に言っちゃうし )

.. _gearbox: https://pypi.python.org/pypi/gearbox/0.0.2
.. _PasteScript: https://pypi.python.org/pypi/PasteScript
.. _PasteDeploy: https://pypi.python.org/pypi/PasteDeploy

.. author:: default
.. categories:: none
.. tags:: misc,gearbox,paste
.. comments::
