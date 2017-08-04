WSGI向け認証フレームワーク､repoze.whoを使ってみた
=================================================

[repozewwho](https://pypi.python.org/pypi/repoze.who/)
というWSGI用の認証フレームワークがあります｡

Webアプリケーションを書くうえでユーザー認証はほぼほぼ必須となりますが､
それを提供してくれる素晴らしいものです｡

なかなかよくできたやつなのですが､分かるまでが分かりにくいです｡
[repozewwho](https://pypi.python.org/pypi/repoze.who/)
がどんなものか､どう使うのかを中心に､
理解への助けになるものをまとめておこうと思います｡

[pyhack 36](http://connpass.com/event/3554/) でやってたことです｡

repoze.whoで何ができたか
------------------------

WSGIでWebアプリケーションを作る際になどに､認証の枠組みを提供できます｡

実際に今作っている [WeiWei](https://github.com/hirokiky/weiwei)
という(WSGIベース､つまりWebフレームワークなしで作っている)
Wikiエンジンで導入してみました｡
これがその変更点なのでこれをパクれば導入できます:

-   [WeiWei - Added authentication (but the backend is still
    dummy).](https://github.com/hirokiky/weiwei/commit/1b8cf16836c3137130309376b95a51150500b2f0)

導入できたものは「WSGIアプリケーションが401を返したときにBasic認証を促し､
入力された場合は入力が何であれ認証を許可する」もの｡
(WSGIミドルウェアとして
[repozewwho](https://pypi.python.org/pypi/repoze.who/) を設定しています)

ここで注意すべきなのは､この変更点では「利用者の入力が正当なものか」の検証はしていないということ｡
実際の認証処理は
[SillyPlugin](https://github.com/hirokiky/weiwei/commit/1b8cf16836c3137130309376b95a51150500b2f0#diff-3d801352b1480a5613820fe60e5c45e0R7)
が担当しているけど､ここではユーザーの入力があればとりあえず「認証OK!!!」としています｡
(Sillyなんです)｡ この変更点はあくまで
[repozewwho](https://pypi.python.org/pypi/repoze.who/)
の導入であり「ユーザーの入力が正しいかどうか」を判定する処理は
書いていない｡

言ってしまうと [repozewwho](https://pypi.python.org/pypi/repoze.who/)
はそんなものを提供するものじゃないです｡

repoze.whoって何よ
------------------

[repozewwho](https://pypi.python.org/pypi/repoze.who/) が
[django.contrib.auth](https://docs.djangoproject.com/en/dev/topics/auth/)
のような「なんかユーザーモデルとかあるやつ」じゃないと分かったところで､
[repozewwho](https://pypi.python.org/pypi/repoze.who/) とは何なのよ｡

簡単に言うと「単なる枠組み」｡
そこに付随する処理(例えば上記した､実際の認証をする'Authenticator')を自分たちで
プラグインとして提供できます｡
[repozewwho](https://pypi.python.org/pypi/repoze.who/)
自体が提供するプラグインもあるけど､根幹としてある
[repozewwho](https://pypi.python.org/pypi/repoze.who/) は枠組み/
流れのみを提供してくれるものです(美しい)｡

[repozewwho](https://pypi.python.org/pypi/repoze.who/)
がプラグインを差し込む場所として提供しているのは以下4点:

-   Identifier: 認証情報を取り出す部分
-   Authenticator: 認証を実際にする部分
-   MetadataProvidor: 認証時に取れた付加情報を追加する部分
    (今回は使ってない)
-   Challenger: 認証を促す部分

その差し込んだプラグインが､
[repozewwho](https://pypi.python.org/pypi/repoze.who/)
をWSGIミドルウェアとして使うことで適宜呼び出されて
いきます([repozewwho](https://pypi.python.org/pypi/repoze.who/)
が提供するAPIで明示的に呼ぶこともできますヾ(*´∀｀*)ﾉｷｬｯｷｬ)｡

それぞれの説明は､ [repozewwho](https://pypi.python.org/pypi/repoze.who/)
コントリビューターでもあるaodagさんのブログ記事も参考に なります｡

-   [repoze.whoを調べてみた](http://blog.aodag.jp/2009/12/repozewho.html)

使ったプラグインとその説明
--------------------------

今回使ったプラグインと､
[repozewwho](https://pypi.python.org/pypi/repoze.who/)
の実際の動きについて少々｡

今回の場合は以下のプラグインを使いました:

-   Identifier: basicauth (repoze.who提供)
-   Authenticator: silly (自前の仮置き)
-   Challenger: basicauth (repoze.who提供)

### Identifierとしてのbasicauth

Basic認証から認証情報(identity)をとりだします｡
identity['login']とidentity['password']からクライアントの入力をとれます
(このidentityは例えばAuthentiatorなどで使われる)｡

### Challengerとしてのbasicauth

クライアントに対してBasic認証をさせるヘッダを送信する｡

他のChallengerとしてはredirectorなどがあり､ログイン画面にリダイレクトさせたりできます｡

### Challengerの発火

ちらっと説明したように､Challengerが発火するタイミングは 「
[repozewwho](https://pypi.python.org/pypi/repoze.who/)
配下のWSGIアプリケーションが401を返したとき」としています｡

この挙動はwho.iniに記述したgeneralセクションの
[challenge\_decider](https://github.com/hirokiky/weiwei/commit/1b8cf16836c3137130309376b95a51150500b2f0#diff-3d801352b1480a5613820fe60e5c45e0R7hh)
による挙動で､そこは適宜変更できます｡

今回の場合はloginを担当するWSGIアプリケーションから「ユーザーがログインしていなければ
401を返す」ようにしてChallengerを呼び出させています｡
が､やりかたとしてはあんまり賢くないですね｡今後改良することになりそうです｡

-   [login
    view](https://github.com/hirokiky/weiwei/commit/1b8cf16836c3137130309376b95a51150500b2f0#diff-3d801352b1480a5613820fe60e5c45e0R7hh)

(余談:
login\_viewはDjango/Pyramidのviewと同じ立ち位置のもので､WSGIアプリケーションとしてはweiwei.web.login\_dispatchというものが担当しています｡これは
[WeiWei](https://github.com/hirokiky/weiwei) の実装の話です)

サードパーティのプラグイン
--------------------------

プラグインとしては優秀そうなものがいくつかありますが､あまりメンテされてない印象｡
たいていのものは repoze.who 1.0 系にのみ対応したものになるならしい｡
以下の3つはなかなか優秀そうであるけど､repoze.who
2系に対応してないくさいとか Python
3系に対応してないとかで使用しなかったものです:

-   [repoze.wo\_sqlalchemy](https://github.com/repoze/repoze.who-sqlalchemy)
    :バックエンドをSQLAlchemyとしてUserModelとかをうまく扱ってくれるっぽい(UserModel!!)
-   [repoze.what](http://what.repoze.org/docs/1.0/):
    認証したうえでの権限の扱いなどをしてくれるものっぽい
-   [repoze.who-use\_beaker](https://pypi.python.org/pypi/repoze.who-use_beaker/)
    :
    repozeが提供する認証を保持するauth\_tktというプラグインがCookieベースでいけてないので､それをbeaker
    (セッション)でやろうというもの｡

まぁ使いたければメンテしてやるか参考にして自分で作ればいいです｡

[repozewwho](https://pypi.python.org/pypi/repoze.who/)
は美しい(プラグインを基本としてその枠組みのみ提供するとこが良い)ですが､
どうにもプラグインとして外部に提供させると使う側としては選別が面倒になりますね｡

さっき書いたSillyPluginではあまにり滑稽なので､その後にちゃんとUserモデルを使った
認証を [WeiWei](https://github.com/hirokiky/weiwei) では書いています

-   [WeiWei - Added User/Roll models and authentication by using
    them.](https://github.com/hirokiky/weiwei/commit/3303a4d10cb1740f6356643138d235e95353198a)

まぁ自分でプラグインを書いていきましょう｡

まとめ
------

結局 [repozewwho](https://pypi.python.org/pypi/repoze.who/)
とは､認証の流れを提供するだけのものでした｡
プラグインを開発者が提供して初めて期待する「認証」ができます｡
[repozewwho](https://pypi.python.org/pypi/repoze.who/) は .ini.
の記述によるプラグイン設定､zope.interfaceによるプラグインの実装
など非常に美しいものでした｡

「ユーザーモデルとかあってDBに保存するもの欲しい」という人はプラグインを探すか
自分で書くか､WSGIだけというのは諦めて優秀なWebフレームワークのDjangoとかを使いましょう｡

