Python3.4のSingle-dispatchで遊んでみた - Python Advent Calendar 2013
=================================================================================

`Python Advent Calendar 2013 <http://www.adventar.org/calendars/166>`_ の 1 日目を担当します､ :tw:`@hirokiky` です｡
`昨年 <http://connpass.com/event/1439/>`_ に引き続きPythonAdventCalendarを主催しています｡

ちなみに今年は現時点であと 4 人参加者が足りません！ぜひご参加ください

* `Python Advent Calendar 2013 <http://www.adventar.org/calendars/166>`_

さて､今年の Python Advent Calendar のテーマは 'Not Web' ということなので､
Web に限らない話を書きます｡

つい最近の11月24日に `Python 3.4 <http://www.python.org/download/releases/3.4.0/>`_
の Beta 1 がリリースされました｡
Python 3.4 b1 には無事 ensure-pip も入り､いよいよ Python 3 感が増してきました｡
そんなところですが､ここで Python 3.4 から入る single-dispatch_ で遊んでみました｡

single-dispatchって何?
----------------------------
single-dispatch_ は functools の一つで､第一引数の型に応じて処理を変更する
generic function を作るものです｡

単純な例で試してみます:

.. code-block:: python

    from functools import singledispatch
    
    @singledispatch
    def fun(arg):
        return 'default'
    
    ## Registering behaviors to correspond to each types
    
    @fun.register(int)
    def fun_int(arg):
        return 'int'
    
    @fun.register(list)
    def fun_list(arg):
        return 'list'

    assert fun(3) == 'int'
    assert fun([]) == 'list'
    assert fun('str') == 'default' # str type is not registered.

    assert fun_int('dummy') == 'int'
    assert fun_list('dummy') == 'list'
    assert fun(object()) == 'default'  # Using 'instance of object' to test the default behavior.
        

ポイント:

* `singledispatch` によって generic function を定義
* .registerによって､型と対応する処理を登録
* 通常の関数のように generic function を呼び出す

アンチパターン
----------------------

さきほどあげた例では少し簡単すぎるので､利点が伝わりにくいかもしれません｡
なのでここで､アンチパターンをあげておきます:

.. code-block:: python

    def fun(arg):
        if isinstance(arg, int):
            return 'int'
        elif isinstance(arg, list):
            return 'list'
        else:
            return 'default'
    
    assert fun(1) == 'int'
    assert fun([]) == 'list'
    assert fun('str') == 'default'

ダメなポイント:

* 新しい型と対応する処理を後から追加できない
* 各型に応じた処理を取り出せない
  (先の例でいう 'fun_int' などを直接テストできない)
* 見難い

などですね｡

他にもいくつか挙動を試していますが､長くなるので気になる人は
Githubのリポジトリ にあげてるのでそちらを参照してください｡

* `hirokiky/demo_singledispatch:trial.py <https://github.com/hirokiky/demo_singledispatch/blob/master/trial.py>`_

実用例を考えてみた
-------------------------

さてこの single-dispatch_ ､たしかに面白いですが何に使えるでしょうか｡
一つ考えてみたのは **「任意の型で返り値を返す関数たちについて､返り値を共通の型で包む」** というものです｡ちょっと自分で言っててもよく分からないので､例を考えます｡

チャットするロボットを考えます｡人間とロボットはMessageというオブジェクトでやりとりするとしましょう｡
ロボットは単なるcallableで､Messageオブジェクトを受け取りMessageオブジェクトを返します｡
ただ実装上､このロボットが返す値をいちいちMessageオブジェクトにしてやるのは面倒なので､
ロボットから返す値をMessageオブジェクトに変換する処理を挟んでやります｡
この「何らかの型」 => 「Messageオブジェクト」の変換処理をgeneric functionとして持つわけですね｡「共通の型」というのがこのMessageオブジェクトです｡

さてまずはロボットの実装に必要な､ライブラリとしての処理を実装します｡

* Message: ロボットとのやりとりに使うオブジェクトのクラス
* generate_message: 各型 => Messageに変換するgeneric function
* as_robot: 関数をロボットとして定義するデコレーター

.. code-block:: python

    class Message(object):
        """ Messages to communicate each robots.
        """
        def __init__(self, body, **metadata):
            self.body = body
            self.metadata = metadata
    
        def __str__(self):
            return '''\
    {self.body}
    * metadata: {self.metadata}
    '''.format(self=self)
    
    @singledispatch
    def generate_message(arg):
        """ Creating Message object for each types.
        """
        raise TypeError('Unexpected type')
    
    @generate_message.register(str)
    def str_to_message(arg):
        return Message(arg)
    
    @generate_message.register(dict)
    def dict_to_message(arg):
        body = arg.pop('body')
        return Message(body, **arg)
    
    def as_robot(func):
        def wrapped(*args, **kwargs):
            ret = func(*args, **kwargs)
            return generate_message(ret)
        return wrapped


さてこれでロボットを実装する準備ができました｡
ここまでをライブラリ､フレームワーク側から提供されるべきものと想定しています｡
以下はそれを利用した､ユーザー側が書くべき処理の例です:

.. code-block:: python

    @as_robot
    def antique(message):
        return "Good morning, Master Ren."
    
    @as_robot
    def neomodel(message):
        return {'body': "I'm here.",
                'enjoyment': 1}
    
    if __name__ == '__main__':
        print('antique::', end=' ')
        print(antique('dummy message'))
        print('neomodel::', end=' ')
        print(neomodel('dummy message'))


実装できました｡

antique関数では文字列を直接返し､neomodel関数では辞書を返しています｡
各関数はas_robotというデコレーターで包まれているので､戻り値がMessageオブジェクト
で共通になります｡

実行してやるとこんな答えが返ります::

    antique:: Good morning, Master Ren.
    * metadata: {}

    neomodel:: I'm here.
    * metadata: {'enjoyment': 1}

まぁこんなかんじで､任意の型 => 共通の型への変換処理を作るのにも使えるのではないか
という例でした｡
もちろんロボット関数がMessageオブジェクトを返した場合や､後から変換処理を追加する
ことも考えられます｡これもアップロードしてあるファイルから見てみてください｡

* `hirokiky/demo_singledispatch:chatbot.py <https://github.com/hirokiky/demo_singledispatch/blob/master/chatbot.py>`_ 

ただこの例の場合as_robotデコレーターを外せないので､純粋な関数としての
テストが難しいです｡そこはas_robotデコレーターを取り外し可能にするなどして
対応するのが良いかもしれません｡

まとめ
-----------

* single-dispatch_ 面白い
* 任意の型 => 共通の型 への変換などに使えそう

小さいながらも面白い機能で､とくにフレームワークやライブラリを提供するときに
使えそうな印象です｡

遊びで書いたコードはここにおいていますので､より詳しくは読んでみてください:

* https://github.com/hirokiky/demo_singledispatch


以上です｡2日目は露木さん(:tw:`@everes`)にお願いしたいと思います｡

* `Python Advent Calendar 2013 <http://www.adventar.org/calendars/166>`_

.. _single-dispatch: http://www.python.org/dev/peps/pep-0443/

.. author:: default
.. categories:: none
.. tags:: python,pythonadventcalendar,single-dispatch
.. comments::
