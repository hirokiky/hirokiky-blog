Pyramidでzope.interfaceを使う
=============================

[Pyramid](http://docs.pylonsproject.jp/projects/pyramid-doc-ja/en/latest/index.html)
で [zope.interface](https://pypi.python.org/pypi/zope.interface/4.0.5)
を使うときの話

-   [zope.interface](https://pypi.python.org/pypi/zope.interface/4.0.5)
-   [Pyramid](http://docs.pylonsproject.jp/projects/pyramid-doc-ja/en/latest/index.html)
    における
    [zope.interface](https://pypi.python.org/pypi/zope.interface/4.0.5)
    の使い方
-   お作法

zope.intreface
--------------

zope.intreface\_ が面白いと最近知った。

実装を持たない「インターフェース」だけ定義して、
そこに後で実装をもたせる、というかんじ(超ざっくり)

折れ線グラフとしてのインターフェースをILinechart、
なにやら描画してくれるインターフェースをIRendererとして考えてみる。:

``` {.sourceCode .python}
import zope.interface


class IRenderer(zope.interface.Interface):
    def render():
        """なんか描画する的"""


class ILinechart(zope.interface.Interface):
    series = zope.interface.Attribute("""Y軸の値みたいな""")
    category = zope.interface.Attribute("""X軸の値みたいな""")


@zope.interface.implementer(IRenderer)
class LinechartRenderer(object):

    __used_for__ = ILinechart

    def __init__(self, context):
        self.context = context

    def render(self):
        return str(zip(self.context.series, self.context.category))
```

こんなかんじか。 嬉しいのは:

-   インターフェースだけ先に書いといて実装はあとから持たせられる。
-   継承みたいに書いた時点で関係が固定されるわけじゃない。

とかかな。正直面白半分で使い始めてる節あるので何とも。

[zope.interface](https://pypi.python.org/pypi/zope.interface/4.0.5)
のドキュメント読んでこのへん読んどけば捗りそう:

-   [Using the Adapter
    Registry](http://docs.zope.org/zope.interface/human.html)
-   [A Comprehensive Guide to Zope Component
    Architecture](http://www.muthukadan.net/docs/zca.html)

Pyramidにおけるzope.interfaceの使い方
-------------------------------------

[zope.interface](https://pypi.python.org/pypi/zope.interface/4.0.5) とか
[zope.component](https://pypi.python.org/pypi/zope.component)
でもアダプターの登録ができますが、 それは
[Pyramid](http://docs.pylonsproject.jp/projects/pyramid-doc-ja/en/latest/index.html)
でやりましょう。

[Registry](http://docs.pylonsproject.org/projects/pyramid/en/1.0-branch/api/registry.html#module-pyramid.registry)
というものに登録する。 まぁconfigとかrequestにひっついてるから:

``` {.sourceCode .python}
# config 経由で登録して
config.registry.registerAdapter(LinechartRenderer,
                                (ILinechart,), IRenderer, '')

# request 経由で取ると
adapted = request.registry.getAdapter(linechart, IRenderer, '')

# 良い感じ
adapted.render() # '[(0, 0), (1, 1), (2, 4)]' とか
```

でまぁ:

``` {.sourceCode .python}
config.registry.registerAdapter(LinechartRendererJSON,
                                (ILinechart,), IRenderer, 'json')
config.registry.registerAdapter(LinechartRendererHTML,
                                (ILinechart,), IRenderer, 'html')
```

とかname変えて登録していくとさらに良い
(この場合rendererのメソッドにもたせたほうがいいかもだけど)。

お作法
------

そんな感じで、アプリケーション書くときに

-   \_\_init\_\_.pyでconfig.registry.registerAdapter
-   view\_callable内でrequest.registry.getAdapter

とかやっちゃうわけだけど、これはまぁ行儀良くないらしい。
[zope.interface](https://pypi.python.org/pypi/zope.interface/4.0.5)
を意識しないといけないのはライブラリやフレームワークの開発者であって
ユーザー(ライブラリ等を使って開発する人)には見せたくないとのこと[要出典]。

まあたしかに、もっとわかりやすい書き方してよって気になるしね。

そのお行儀の良い書き方というのは簡単で:

-   config.add\_directiveでdirectiveを追加
-   そのdirective経由でアダプター登録
-   requestを受け取るAPIとしての関数から、アダプトされたオブジェクトを返す

というもの。 さらに [venusian](https://pypi.python.org/pypi/venusian)
を使って、directive経由でアダプター登録してたのを
デコレーターで書いてやることができる。

まぁこんなかんじになるのか(renderer\_configとto\_rendererは自分で書くのよ):

``` {.sourceCode .python}
@renderer_config('',
                 chart_type='linechart')
def str_linechart(linechart):
    return str(zip(linechart.series, linechart.category))

renderer = to_renderer(linechart, '')
renderer.render()
```

このrenderer\_configのなかではconfig.set\_rendererとか呼び出して登録してやるといい。
さながらPyramidのview\_configとconfig.add\_viewみたいなもんである。

まあ一見に如かずなのでrebecca.todictを読めばいいと思う:

-   <https://github.com/rebeccaframework/rebecca.todict>

これを参考に私もpyramid\_tochartというのを書いてるので、こっちも参考になるかも:

-   <https://github.com/hirokiky/pyramid_tochart>

ただまあ良い書き方を模索してるところ

