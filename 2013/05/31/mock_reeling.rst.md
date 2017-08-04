恐怖のMocking
=============

Python と Mock の話。

import されているモジュールをモックに差し替えたいときに

``` {.sourceCode .python}
from minimock import Mock

import hoge

hoge.fuga = Mock('hoge.fuga')
```

と安易にやってはいけない。 これだと restore しても元の hoge.fuga
には戻らない。

例えば
------

turtle.py というのを考える:

``` {.sourceCode .python}
# turtle.py

def soup():
    return 'turtle'
```

tests.py も考える。

``` {.sourceCode .python}
import unittest

from minimock import Mock, restore

import turtle


class TurtleTest(unittest.TestCase):
    def setUp(self):
        turtle.soup = Mock('turtle.soup',
                           returns=u'mock turtle')

    def tearDown(self):
        restore()

        print turtle.soup()

    def test_soup(self):
        self.assertEqual(turtle.soup(), 'mock turtle')


if __name__ == '__main__':
    unittest.main()
```

と restore までしてやるわけだけど、悲しいことに(あたいまえなことに)
turtle.soup はモックのままである。

``` {.sourceCode .sh}
% python -m tests
```

> Called turtle.soup() Called turtle.soup() mock turtle .
> ----------------------------------------------------------------------Ran
> 1 test in 0.000s
>
> OK

よくやるのがテスト対象のモジュール target に対して、そこで import
されている turtle.soup を差し替えるというもの。 target.turtle.soup
をモックに差し替えるんだろうけど、その場合も元に戻っていないと厄介。

mock 使おう
-----------

Mock 使わずに mock 使えばいい。

``` {.sourceCode .python}
from minimock import mock, restore

...

class TurtleTest(unittest.TestCase):
    def setUp(self):
        mock('turtle.soup',
             returns=u'mock turtle')

...
```

これだけ。ほんと素直に mock 使おう。restore すれば元に戻ってくれる。
呼び出してみても restore されてるのが分かる。

``` {.sourceCode .sh}
% python -m tests
```

> Called turtle.soup() turtle .
> ----------------------------------------------------------------------Ran
> 1 test in 0.018s
>
> OK

修正したいが名前変えたくない場合
--------------------------------

TraceTracker などで呼び出しを追ってる場合、モックするときの名前も
重要になる。

既存テストが副作用起こしてて修正したい！けど名前変えたくない！ってときは
mock\_obj 引数使ってこうしよう

``` {.sourceCode .python}
# turtle.soup = Mock('turtle_soup')

mock('turtle.soup'
     mock_obj=Mock('turtle_soup'))
```

わあい

これで万事解決。

小ネタ
------

モック呼びだされるたびに標準出力されると、テストの結果がみにくくなる:

``` {.sourceCode .sh}
................F.......F..........Called turtle.soup
.......Called turtle.soup
Called turtle.soup
Called turtle.__add__
Called turtle.__add__
Called turtle.soup
...
Called turtle.soup
Called turtle.soup
.......E...s...
```

iMac を窓の外に放り投げる前に、 tracker=None にして落ち着こう。

``` {.sourceCode .python}
Mock('turtle.__add__',
     returns='Ambition',
     tracker=None)
```

これで黙る。

まとめ
------

-   minimock.Mock はすぐ消えるオブジェクトに適応
-   モックで頑張るな

