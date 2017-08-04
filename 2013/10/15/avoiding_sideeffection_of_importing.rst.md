Pythonにおける「許されざる悪事」を避けるために
==============================================

許されざる悪事というものが存在する

-   [許されざる悪事](http://www.gembook.org/2011-05-11.html)

モジュールを import するだけでグローバルな値が設定されるというもの｡

    「import の順番に依存した処理ほど不愉快なものはない。こういった依存性を持つ処理は非常に脆弱で、ちょっとしたことですぐエラーとなってしまい、メンテナンスしにくいコードになってしまうものなのである。」

ぁっぉ

やってみよう
------------

importに依存しない処理を書いてみましょう:

``` {.sourceCode .python}
# In mymodule.py

hoge = None

def setup_hoge():
    global hoge
    hoge = 'hoge'
```

としてアプリケーションの設定をする処理のうちにsetup\_hogeを呼び出してやります｡
(まぁ paste.app\_factory に指定する main 関数とかそんなとこで呼ぶ)

だめだった
----------

でもここで､別のモジュール内でhogeが直接読まれてるとちょっと問題がありました｡
直接っていうのはこういうこと:

``` {.sourceCode .python}
from mymodule import hoge  # Forever None
```

このモジュールがひょんなことで読み込まれていると､このモジュール内でのhogeは
setup\_hogeが呼ばれてもNoneのままです｡

setup\_hogeが書き換えるのはあくまでmymodule内のhogeなので､別モジュールに読んでいると
そっちまでは反映されないんですね｡当たり前っちゃそうか｡

解決
----

というわけで間接的に使います:

``` {.sourceCode .python}
import module

module.hoge
```

もしくは取得用の関数を用意する:

``` {.sourceCode .python}
# In mymodule.py

hoge = None

def get_hoge():
    return hoge

def setup_hoge():
    ...
```

これでうまくいきそうです

すぺしゃるさんくす
------------------

nakanurayさんとpodhmoさんが教えてくれました

podhmoさんが書いてくれたgistがわかりやすい

ありがとうございます

追記
----

[zope.proxy](https://pypi.python.org/pypi/zope.proxy) というのを使うと
もっと綺麗/安全に書けるようです

-   <https://gist.github.com/aodag/7095341>

すごい｡これならhogeをうっかり直接使っても問題ないですね｡
aodagさんが教えてくれました｡あzさzs

