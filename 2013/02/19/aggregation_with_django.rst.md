Djangoで売上を集計/集約処理する
===============================

集計/集約します。
(この記事ではaggregationを「集計」と訳します。集約、よりもヒットしやすそうだったから)

Django 1.4 日本語ドキュメント(暫定) ではこちらにあります。

-   [集約(Aggregation) - Django 1.4
    documentation](http://docs.djangoproject.jp/ja/latest/topics/db/aggregation.html)

ちなみにDjangoでaggregationの機能が追加されたのは1.1からですね。 ([1.1
リリースノート](https://docs.djangoproject.com/en/1.4/releases/1.1/#aggregate-support))。

正直、上記のドキュメント読めば集計処理の大体は網羅できると思います。
ですが今回はユースケースにあわせて、集計の機能を紹介します。

今回は

-   売上合計金額/件数の算出
-   円グラフの算出
-   ランキングの算出
-   折れ線グラフの算出

をやってみます。 これくらいの項目があれば十分ですかね。
お店の売れ行きや人気商品は掴めると思います。

さて、今回考えてみるのはお人形屋さんです。
とあるお人形屋さんの売上情報をもとに、集計処理を行なって行きましょう。

想定するデータ構造
------------------

はじめにテーブル/Djangoのモデルからみていきましょう。

必要なモデルは2つで、お店に並ぶ商品と売上情報です。
models.pyは以下のようになると思います。

``` {.sourceCode .python}
from django.db import models
from django.contrib.auth.models import User

class Item(models.Model):
    """お店の商品
    """
    name = models.CharField(u"商品名", max_length=255)
    price = models.PositiveIntegerField(u"価格")


class SalesHistory(models.Model):
    """売上情報
    """
    item = models.ForeignKey('Item')
    user = models.ForeignKey(User)
    sold_datetime = models.DateTimeField(u"販売日時")
```

こんなところかと。 では早速集計をしていきましょう。

> **note**
>
> 今回は省きますが、Itemには他にも商品のカテゴリや販売元などのフィールドが追加できると考えられますね。
> 例えば販売元ごとに折れ線グラフを集計してやることで「今どこの販売元の商品が注目されているか」を
> みることができちゃうと思います。

売上合計金額/件数の算出
-----------------------

簡単なところから、売上合計金額と件数です。

集計の対象期間は「今月」として話をすすめます。つまり今月の初日から昨日までが対象です。
まずはその期間内のSalesHistoryを取得してみましょう。

``` {.sourceCode .python}
>>> import datetime
>>> from dateutil.relativedelta import relativedelta

>>> today = datetime.date.today()
>>> first_of_thismonth = today + relativedelta(day=1)

>>> from dollshop.models import SalesHistory
>>> SalesHistory.objects.filter(sold_datetime__range=(first_of_thismonth, today))
```

こんなところと思います。
当日のdate、relativedeltaを使って月の初日(first\_of\_thismonth)をとりました。
最後の行でSalesHistoryをfilterしてます。

``` {.sourceCode .python}
>>> sales_of_thismonth = SalesHistory.objects.filter(sold_datetime__range=(first_of_thismonth, today))

>>> # 売上件数
>>> sales_of_thismonth.count()

>>> # 売上金額
>>> from django.db.models import Sum
>>> sales_of_thismonth.aggregate(Sum('item__price'))
```

簡単ですね。 aggregateを使います。lookupはSum。

円グラフの算出
--------------

``` {.sourceCode .python}
>>> # 商品ごと
>>> sales_of_thismonth.values('item').annotate(total_price=Sum('item__price'))

>>> # ユーザごと
>>> sales_of_thismonth.values('user').annotate(total_price=Sum('item__price'))
```

商品ごとなりユーザごとの合計金額を求めて、アプリ側で各々の金額を全部の合計金額で割れば比率はでますね。
まぁ大抵のJSライブラリはそのまま値で渡せばよしなにやってくれます。

この例では「商品ごと」というなんともビミョーな円グラフですが、応用すれば「商品のカテゴリごと」などもできますね。

ランキングの算出
----------------

``` {.sourceCode .python}
>>> # 金額順
>>> sales_of_thismonth.values('item').annotate(total_price=Sum('item__price')).order_by('-total_price')

>>> # 件数順
>>> sales_of_thismonth.values('item').annotate(numof_sales=Count('id')).order_by('-numof_sales')
```

さきほどの円グラフの算出にそのままorder\_byをつけただけです。
'-'を付けてるのは降順にするためです。

折れ線グラフの算出
------------------

折れ線グラフというのは横軸に日時、縦軸に売上金額(もしくは件数)をとったものを考えます。
日付はある単位ごとにまとめたものになりますね。例えば日毎の売上、月毎の売上などです。
1年間の売上推移を見るのに日毎の集計をしちゃったら、横に365点とることになるんで非常に見難いですよね。

``` {.sourceCode .python}
>>> # 日毎
>>> sales_of_thismonth.extra({'sold_date': 'strftime("%%Y%%m%%d", sold_datetime)'}).values('sold_date').annotate(total_price=Sum('item__price'))

>>> # 月毎
>>> sales_of_thismonth.extra({'sold_month': 'strftime("%%Y%%m", sold_datetime)'}).values('sold_month').annotate(total_price=Sum('item__price'))
```

extraで、values-annotateのグループ化に使う値を作ってあげてます。
日毎の集計ならstrftimeじゃなくてdateでもいけそう。

おわりに
--------

意外とあっさりできましたが、理解するのはちょっとややこしいかも。
annotateの前にvaluesをおいてグループ化してやってるわけですが、SQL脳でみると.group\_byなどのメソッドが欲しいなというところ。

これについては何度も議題にあがってるようです。

-   <https://groups.google.com/d/topic/django-developers/dUlwp9z9gGI/discussion>

よりORMらしい方法(values-annotateのことかな)が採択されたとのこと。詳細は
[チケット3566](https://code.djangoproject.com/ticket/3566)
のようで、このチケットは「ORMで集計できるようにしよう」という提案のようですね。
さっと見てみたところ、もともとの提案ではgroup\_byなどであったようですが、まぁ時間のあるときにでもじっくり見ときましょ。

