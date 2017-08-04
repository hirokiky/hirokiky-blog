PasteScript空騒ぎ
=================

[PasteScript](https://pypi.python.org/pypi/PasteScript) の話｡

PasteScriptって?
----------------

アプリケーションに必要なコマンドを作る地盤を提供してくれるものです｡
[PasteScript](https://pypi.python.org/pypi/PasteScript)
自身が提供するものでは､テンプレートからアプリケーションを作成する
コマンドがあります:

``` {.sourceCode .sh}
paster create
```

テンプレートを作っておけばアプリケーションがさくっとできます｡

この説明だけだとなんじゃそりゃという感じですが､ここで
[PasteDeploy](https://pypi.python.org/pypi/PasteDeploy) も紹介します｡

[PasteDeploy](https://pypi.python.org/pypi/PasteDeploy)
はWSGIアプリケーションの構成を設定ファイルから指定できるものです｡
例えばDBの設定やLoggingの設定をファイルに記述して､その設定を元に
アプリケーションを構築､起動できます｡

この [PasteDeploy](https://pypi.python.org/pypi/PasteDeploy) と
[PasteScript](https://pypi.python.org/pypi/PasteScript)
を併用することで以下のようなコマンドで
簡単にWSGIアプリケーションが起動できたりします:

``` {.sourceCode .sh}
paster serve
```

この serve コマンドも
[PasteScript](https://pypi.python.org/pypi/PasteScript)
の提供するもので､
[PasteDeploy](https://pypi.python.org/pypi/PasteDeploy) の
機能を使って簡単にWSGIアプリケーションを構築､起動できます｡
(必要な設定はdevelopment.iniのようなiniファイルに書いておきます)

WSGIアプリケーションで開発するにも Django でいう management command とか
startapp が欲しくなりますが､まぁ
[PasteScript](https://pypi.python.org/pypi/PasteScript)
はそんなとこです｡

PasteScript空騒ぎ
-----------------

その [PasteScript](https://pypi.python.org/pypi/PasteScript)
､WSGIアプリケーションやWebフレームワークで広く使われており
デファクトスタンダードといっても良いくらい有用なものですが､一つ大きな問題がありました｡

Python3 に対応していないということです｡(あぁレガシー)｡

で一番困るのは [PasteScript](https://pypi.python.org/pypi/PasteScript)
を使っていた各Webフレームワークたちです｡
Webフレームワークも「よしPython3に対応するか」と思うわけですが､
依存するパッケージがPython3に対応していないと話になりません｡

ここで各フレームワークが頑張って脱
[PasteScript](https://pypi.python.org/pypi/PasteScript) を模索します｡

Pyramid はその内部に
[PasteScript](https://pypi.python.org/pypi/PasteScript)
を取り込み/修正し､ pcreate などの
コマンドを提供しました(Python3対応をしている
[PasteDeploy](https://pypi.python.org/pypi/PasteDeploy)
はPyramid1.4の現在でも使われています)

Turbogears は [gearbox](https://pypi.python.org/pypi/gearbox/0.0.2)
を用意しました｡これは
[PasteScript](https://pypi.python.org/pypi/PasteScript) の代替になる
パッケージで､ Python3 でも動作します｡

いっぽうDjangoは [PasteScript](https://pypi.python.org/pypi/PasteScript)
に依存せず独自のmanagement commandという地盤と project/application
templateのしくみを提供していたので､全く関係ありませんでした｡

gearboxおすすめ
---------------

さてさて [PasteScript](https://pypi.python.org/pypi/PasteScript)
が使えない今､WSGIアプリケーションを作る際には どうしたものでしょうか｡

[gearbox](https://pypi.python.org/pypi/gearbox/0.0.2) を使いましょう｡

これはTurboGears2がPython3対応する際にできたパッケージですが､TurboGearsに無関係な
WSGIアプリケーションでも使えます｡ついでに
[PasteDeploy](https://pypi.python.org/pypi/PasteDeploy) に依存して serve
用の コマンドも直接提供してくれます:

``` {.sourceCode .sh}
gearbox serve
```

[gearbox](https://pypi.python.org/pypi/gearbox/0.0.2)
はどのWSGIアプリケーションでも使えて素晴らしいです｡
[WeiWei](https://github.com/hirokiky/weiwei) というWebフレームワークレス
なWSGIアプリケーションを最近書いたのですが､これでも使わせてもらってます｡

ついでにScirptとかDeployとかややこしいのをマルっと一つにしてるあたりも分かりやすいです｡

[gearbox](https://pypi.python.org/pypi/gearbox/0.0.2)
素晴らしい｡WSGIアプリケーション+Python3を書くならこれを使うのを薦めます｡

PasteScriptのPython3対応
------------------------

さてPython3非対応でお騒がせした
[PasteScript](https://pypi.python.org/pypi/PasteScript) ですが､
つい最近今年の 9 月にようやく Python3 に対応したようです!!

すごい!!やった!!!!

と思いきや､残念なことに 2to3 を前提としています｡

-   <https://bitbucket.org/ianb/pastescript/pull-request/5/python-3-support/activity>

まぁこれは使いたく無いですね｡
classifierも書き換えられてないしインストール時に2to3がかかるようにもしてないようです｡
([gearbox](https://pypi.python.org/pypi/gearbox/0.0.2)
は現時点でまだ0.0.2ですが Python3 でも使えるいいこですよ٩(๑❛ᴗ❛๑)۶)

このマージを見つけたときは興奮しましたがちょっと違う｡
[PasteScript](https://pypi.python.org/pypi/PasteScript)
をPython3対応をちゃんとさせたいんだという人は
aodagさんのプルリクエストを修正/レビュー/応援すると良いと思います｡

-   <https://bitbucket.org/ianb/pastescript/pull-request/6/support-py3/diff>

私は [gearbox](https://pypi.python.org/pypi/gearbox/0.0.2)
の恩恵に甘えます｡

まとめ
------

[gearbox](https://pypi.python.org/pypi/gearbox/0.0.2) 使いましょう｡

余談
----

世の中に入門記事ばっかり溢れてもつまらないので､ちょっとした小ネタを書きました｡
[パッケージの歴史の話](https://gist.github.com/knzm/5185369)
とかも結構面白い
ので､こういうどうでもよくないどうでもいい記事が増えればいいと思います｡

([PasteScript](https://pypi.python.org/pypi/PasteScript) や Pyramid
のPy3対応については人から聞いた話も多いので
間違ってたら教えてください｡たまに
[PasteDeploy](https://pypi.python.org/pypi/PasteDeploy)
と逆に言っちゃうし )

