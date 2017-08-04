Djangoのテストで設定 (settings) を上書きする
============================================

[Django](https://pypi.python.org/pypi/Django/1.5)
のユニットテストを書くときに、 設定ファイル (settings.py)
を一部変更したいことがある。

そのやり方をメモ。

テストの一部で変えたいとき
--------------------------

ドキュメントに書いてあった。

-   [Override
    Settings](https://docs.djangoproject.com/en/1.4/topics/testing/#overriding-settings)

`django.test.TestCase` を継承したテストで with self.settings(HOGE=1)
とする方法と `django.test.utils.override_settings` デコレーターを使って
@override\_settings(HOGE=1) と する方法があるよう。 `override_settings`
デコレーターはクラスにもメソッドにもかけれる とのこと。

使いどころとしては、例えばキャッシュを使ってる場合に、寿命が切れてる場合の挙動を
テストするとき。キャッシュが切れるまで待つのはいやなので、設定ファイルに
書いてあるキャッシュの寿命を0にしてやってテストする。
手動で消せるかつ、その方法で十分ならそれでもいいけど。

ちなみにこれは 1.4 からの機能。

1.3 以前で変えたい
------------------

setUpで設定ファイル一時保存して、tearDownで戻してあげる。

``` {.sourceCode .python}
from django.conf import settings
from django.test import TestCase

class HogeTest(TestCase):
    def setUp(self):
        self.tmp_settings = settings
        settings.HOGE = 1

    def tearDown(self):
        settings = self.tmp_settings
```

これはちょっと悲しい。

こんなものもあった

-   [django-override-settings](https://github.com/edavis/django-override-settings)

これだと 1.2 - 1.4 で、上記のような `@override_settings`
などが使えるらしい。 でも未検証なので気が向いたら試す。

全体で変えたいときは
--------------------

他の方法としては、テスト用に設定ファイルを分けておくのも良い。
こんなかんじ:

``` {.sourceCode .sh}
settings
|-- __init__.py
|-- common.py
|-- dev.py
|-- prod.py
`-- test.py
```

テスト時のDB設定やログファイルの出力先を変えるときなどはこうする。
大抵の場合は開発用の設定ファイルと同じで十分。

開発用の設定ファイルがリポジトリ管理下に無くて、プロジェクトメンバーが
自由に設定できる場合などは、テスト用の設定ファイルがあるといいかもしれない。

でもまぁ結局「このテストのときだけ値を変えたい」という場合には
対応できない。

Django 1.4 以降を使っているなら `override_settings` を使うのがよさそう。
ただ 「 `settings.test` で値を変更したのにテストに影響がない?!」
とか言って `override_settings` 使ってました、とかはありそう。

使うのを最小限にするのと、設定ファイルはテスト内で上書きされ得ると頭の片隅に置いとくと
いいかも。

参考:

-   [Testing in
    Django](https://docs.djangoproject.com/en/dev/topics/settings/)

