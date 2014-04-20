Pythonでバウンドインナークラスを使う (Python Advent Calendar 2013 最終日)
===========================================================================================

`Python Advent Calendar 2013 <http://www.adventar.org/calendars/166>`_ 25日目のブログ記事です｡

Python Advent Calendarでは例年最終日にはゲストをお迎えして記事を書いてもらっています｡今年は Python 3.4 のリリースマネージャーである Larry Hastings さんに書いていただきました｡

* `Bound Inner Classes For Python <http://momentaryfascinations.com/programming/bound.inner.classes.for.python.html>`_

Larry, thank you very much for your posting to this Python Advent Calendar!

ここでは日本語訳を書いていきたいと思います!

Pythonでバウンドインナークラスを使う
----------------------------------------------

.. In Python, something magic happens when you put a function inside
   a class. If you access that function through an instance of the
   class, you don't simply get the function back. Instead you get
   a new object we call a "method". Conceptually, the function is
   "bound" to the instance; practically, when you call a method,
   it calls the function but automatically passes in the instance
   as the first positional argument, pushing the other positional
   arguments forward by one.

Python において､関数をクラス中に定義した際にはある魔法が起こります｡
クラスのインスタンスを通して関数にアクセスした場合単なる関数が得られるわけではありません｡
代わりに新しいオブジェクト「メソッド (method)」と呼ばれるものが返ります｡
概念的にはこの関数はインスタンスに「バウンド/束縛 (bound)」されています｡
実際にメソッドを呼んだ際にはそのインスタンスが関数の第一引数に自動的に
渡されます｡別の位置引数 (potitional argument)は1つ後ろに追いやられます｡

.. But this magic is only true for functions. Consider classes.
   In Python, if you define a class C inside another class D,
   we call C an "inner class" (or perhaps a "nested class").
   If you access C through an instance of D, nothing magic happens,
   you just get C back, and if you call it the arguments
   to C.__init__ are unchanged.

しかしこの魔法は関数にしか有効ではありません｡クラスについて考えてみましょう｡
Pythonでは､あるクラス C を別のクラス D 内に定義した際に､この C を
「インナークラス (inner class)」と呼びます
(「ネステッドクラス (nested class)」かもしれません)｡
D のインスタンスを通して C にアクセスした場合､
さきほどのような魔法はおこらず､単に C が返されます｡
C を呼び出したとしても C.__init__ への引数は変わりません｡

.. Inner classes are rarely used in Python. Why?
   Because there are no practical advantages to doing so--but
   Python's scoping rules result in some minor inconveniences.
   (See below.)

インナークラスが Python で使われることは稀です｡なぜでしょうか｡
それをする実用的な利点が無いからです｡
Python のスコープルールによって面倒が生じます (後述)｡

.. I often use inner classes in Python anyway, because I have classes
   that conceptually should live inside some other class.
   And, most of the time, these inner classes conceptually want to be
   "bound" to the outer class instance.
   But I have to do it manually, like this:

それでも私は Python でインナークラスをよく使います｡
概念的に他のクラス内にあるべきクラスの場合です｡
大抵の場合インナークラスはアウタークラスに "バウンド" されて欲しいものです｡
しかしこのように､手動でする必要があります:

.. code-block:: python

    class D:
        class C:
            def __init__(self, outer):
                self.outer = outer
    d = D()
    c = d.C(outer)

.. Wouldn't it be nice if inner classes got "bound" the way functions did?
   Wouldn't it be nice if outer was passed in to the inner
   class's __init__ automatically?

関数がするように､インナークラスも "バウンド" されると良さそうです｡
アウタークラスが自動的にインナークラスの __init__ に渡されると良さそうです｡

.. But is this even possible in Python? Some years back
   I asked that question, on Stack Overflow:

でもこれって Python でできることなんでしょうか?
数年前､ Stack Overflow で聞いてみました:

    http://stackoverflow.com/questions/2278426/inner-classes-how-can-i-get-the-outer-class-object-at-construction-time

.. I was amazed at the replies. People not only said that
   it was impossible, but that it was pointless.
   And then Alex Martelli showed it was possible--and his solution
   blew my mind! His solution uses a class decorator,
   and makes your class a "descriptor", which is the same mechanism
   functions use to become methods. Brilliant!

返答には驚きました｡それが不可能というだけでなく､意味がないと言うからです｡
その後に Alex Martelli が､それは可能だと教えてくれました｡
その解法には心底驚かされましたよ!彼の解法はクラスデコレーターを使って､
クラスを「ディスクリプター (descriptor)」にするというものでした｡
これは関数がメソッドになるのと全く同じメカニズムです｡実に素晴らしい!

.. With Alex's permission, I posted this as a "recipe" at the
   Python Cookbook:

Alexの許可を得て､私はこれを「レシピ」として､ Python Cookbookに投稿しました:

    http://code.activestate.com/recipes/577070-bound-inner-classes/

.. I've since fine-tuned the approach many times.
   At this point it works really well! And, yes, the recipe works
   unchanged in both Python 2 and 3.

このアプローチには何度も手直しをしました｡今ではちゃんと動作しますよ!
そして...そうなんです､このレシピは Python 2 と 3 両方で変更なしに動作します｡

.. I love bound inner classes and I use them whenever I can.
   When I tell people about bound inner classes, the most common
   feedback I get is "What's your use case?" My reply:
   "What's your use case for method calls?" I think it's the same
   question. And I suggest to you that bound inner classes are
   just as useful as methods. If not more so!

このバウンドインナークラス (bound inner class) のことは本当に気に入っていますし､
可能であればどこでも使います｡バウンドインナークラスを人に教えたときの
一番よくあるフィードバックは 「それのユースケースは?」ですが､
私の回答はこうです「メソッド呼び出しのユースケースは?」
私が思うに､この2つは全く同じ質問です｡そしてバウンドインナークラスは
メソッドと同じくらい便利なものであるとオススメしておきます｡
もしかしたらそれ以上かもしれません!

.. Of course, classes are more complicated than functions.
   So it makes sense that bound inner classes are more complicated
   than methods. For example, inheritance and more than two levels
   of nesting can make things a little crazy.
   I worked hard to make bound inner classes sensible and predictable
   in these situations--but you have to understand a little
   how they work, and obey some simple rules.
   All of this is documented in the "recipe" above.

もちろん､クラスは関数よりも複雑なものです｡つまりはバウンドインナークラスは
メソッドよりも複雑です｡例えば継承と2レベル以上のネストとなるとちょっと
ややこしいことになります｡
こんな場合でもバウンドインナークラスが実用的で､理解しやくなるよう
考えてみました｡どう動作するかを少し理解して､いくつか単純なルールに従う
必要があります｡これについては､上記の「レシピ」にすべて記載されています｡

.. I hope you start using bound inner classes in your own code!

ぜひ皆さんのコードでもバウンドインナークラスを使ってみてください!


.. In case you're wondering why inner classes can be inconvenient,
   consider the following:

なぜインナークラスが不便になり得るかを疑問に思ってるかもしれませんね｡
以下について考えてみてください:

.. code-block:: python
    
    class D:
        value = 5
        class C:
            value2 = value * 5

.. This code doesn't work; it raises a NameError on value.
   Code in C can't see value--it can't see any members of D.
   All it can see are the members of C, globals, and builtins.
   (Unfortunately the nonlocal keyword doesn't help you here;
   that only helps with nested functions, and class bodies don't
   behave like nested functions.)

このコードは動作しません､ value について NameError を送出します｡
C のコード中では value が見えないのです｡ D のどのメンバーも見えません｡
C が見えるすべてのメンバーはグローバルとビルドインです｡
(残念ながら nonlocal キーワードもここでは助けになりません｡
これはネストした関数でのみ有効で､クラス本体はネストした関数のようには
振る舞いません)

.. If you change it to this:

ここで､コードをこんな感じに変えてみましょう:

.. code-block:: python
    
    class D:
        value = 5
        class C:
            value2 = D.value * 5

.. Sadly that doesn't work either. D doesn't exist yet--technically
   speaking, it "hasn't been bound yet". This code raises a
   NameError on D.

悲しいことに､これもまた動作しません｡
D はまだ存在しないからです｡技術的には､ D は "まだバウンドされていません"｡
このコードは D についての NameError を送出します｡

.. The only thing that works is to do the lookup at runtime:

唯一動作するのは､ルックアップをランタイムで行うことです:

.. code-block:: python

    class D:
        value = 5
        class C:
            def __init__(self):
                self.value2 = D.value * 5

.. In other words, nested classes can't access any members of
   their outer classes at compile-time.

言い換えると､コンパイルタイムにおいてネストしたクラスは
アウタークラスのどのメンバーにもアクセスできないということです｡


.. p.s. To my Japanese readers today on Christmas:
   I want to tell you that Americans don't actually eat KFC on Christmas
   day! This is a marketing gimmick created by KFC in Japan.
   Americans do usually have a big family dinner on Christmas,
   but there's no traditional main course.

追伸､クリスマスを楽しむ日本の読者の皆様へ｡
私が皆さんに教えてあげたいのは､実際にはアメリカ人は KFC をクリスマスに食べないということです!これは日本の KFC が創りだしたマーケティング上のギミックなのです｡アメリカ人は家族でのディナーを盛大に行うものですが､伝統的なメインコースというものはありません｡

以上です｡

Larryさん､ありがとうございました!


(なおフッターにあるCC-BYの表記はこの記事においては有効ではありません｡
The above expression about CC-BY is not available at this entry.)

.. author:: default
.. categories:: none
.. tags:: python,pythonadventcalendar
.. comments::
