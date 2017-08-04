SQLAlchemyのSQL表現言語で集計する
=================================

前回の
[Djangoで売上を集計/集約処理する](http://blog.hirokiky.org/2013/02/19/aggregation_with_django.html)
に続いて、また集計します。

今回はDjango(のORM)ではなく [SQLAlchemy](http://www.sqlalchemy.org/)
を使います。バージョンは0.8。
ただしORMとしてではなく、SQLAlchemyのSQL表現言語(SQLExpression)のみ使います。
(私はSQLAlchemyのド素人で、ORMとして使ったことがないです。ただ、SQLAlchemyの
SQL表現言語が素晴らしいなーと思ったので、試してみました)

SQLExpressionのチュートリアルも参考にしてください:

-   [SQL表現言語チュートリアル (0.6.5
    ドキュメント和訳)](http://omake.accense.com/static/doc-ja/sqlalchemy/sqlexpression.html)
-   [SQL Expression Language Tutorial (0.8
    Documentation)](http://docs.sqlalchemy.org/en/rel_0_8/core/tutorial.html)

前回同様ユースケースにあわせて、集計をしてみます。

今回も:

-   売上合計金額/件数の算出
-   円グラフの算出
-   ランキングの算出
-   折れ線グラフの算出

をやってみます。

さて、今回も考えるのはお人形屋さんです。このお人形屋さんの売上情報、商品の情報
などをもとに集計処理を行なって行きましょう。

想定するデータ構造
------------------

はじめにテーブルから見ていきましょう。

テーブルは3つで、売上情報、商品と商品のカテゴリーです。
以下のようにテーブル定義を書きました:

``` {.sourceCode .python}
categories = Table('categories', metadata,
    Column('id', Integer, primary_key=True),
    Column('name', String),
)
items = Table('items', metadata,
    Column('id', Integer, primary_key=True),
    Column('name', String),
    Column('price', Integer),
    Column('category_id', None, ForeignKey('categories.id')),
)
histories = Table('histories', metadata,
    Column('id', Integer, primary_key=True),
    Column('item_id', None, ForeignKey('items.id')),
    Column('sold_datetime', DateTime)
)
```

-   histories:
    売上情報/履歴。商品へのFK(item\_id)と、その商品が売れた日時
    (sold\_datetime)を持っています
-   items:
    商品。商品名(name)、価格(price)、カテゴリーへのFK(category\_id)を持って
    います。
-   categories: カテゴリー。カテゴリー名を持っています。

売上合計金額/件数の算出
-----------------------

早速集計といきましょう。

``` {.sourceCode .python}
jan = (datetime.datetime(2012, 1, 1), datetime.datetime(2012, 2, 1))

# 2012年1月の売上件数
select([func.count()],
       histories.c.sold_datetime.between(*jan))

# 同期間売上金額
select([func.sum(items.c.price)],
       histories.c.sold_datetime.between(*jan) & \
       (items.c.id == histories.c.item_id))
```

先に `jan` に、betweenに渡す引数を作っています。

円グラフの算出
--------------

円グラフ、GROUP BYしてSUMですね。
せっかくカテゴリーというテーブルを設けたので、カテゴリーごとの売上金額を求め
ましょう。「売上の6割はウィッグなんだねぇ」とかが分かるわけです。
(そうなんだ。すごいね)

``` {.sourceCode .python}
# 2012年1月のカテゴリーごとの売上金額
select([categories.c.name, func.sum(items.c.price)],
       histories.c.sold_datetime.between(*jan) & \
       (items.c.category_id == categories.c.id) & \
       (items.c.id == histories.c.item_id)).\
       group_by(categories.c.id)
```

ランキングの算出
----------------

商品ごとのランキングを算出します。 商品でGROUP
BYしてSUMとって、それでORDER BYですね。
これで1番売上をあげている商品が分かります。

``` {.sourceCode .python}
# 2012年1月の商品ごとの売上金額のランキング
select([items.c.name, func.sum(items.c.price).label('total_price')],
       histories.c.sold_datetime.between(*jan) & \
       (items.c.id == histories.c.item_id)).\
       group_by(items.c.id).\
       order_by('total_price'),
```

`.label()` で商品ごとの売上金額に名前つけて、それを `.order_by()`
で指定して います。 商品が増えてきたら `.limit(100)`
などを追加するのも良いかもしれません。

折れ線グラフの算出
------------------

折れ線グラフというのは横軸に日時、縦軸に売上金額(もしくは件数)をとったものを
考えます。日付はある単位ごとにまとめたものになりますね。例えば日毎の売上、月毎の
売上などです。

``` {.sourceCode .python}
# 全期間の日次売上金額
select([func.date(histories.c.sold_datetime).label('sold_date'),
       func.sum(items.c.price)]).\
       group_by('sold_date'),

# 全期間の月次売上金額
select([func.strftime('%Y-%m', histories.c.sold_datetime).label('sold_date'),
       func.sum(items.c.price)]).\
       group_by('sold_date')
```

[前回](http://blog.hirokiky.org/2013/02/19/aggregation_with_django.html)
では なかなか苦戦した覚えありますが、そうでもない感じですね。

売上がない日/月はそもそも結果にでないのでアプリ側で補完するなり、もっと良い方法
を考えるなりしてください。

おわりに
--------

けっこう分かりやすいし、書きやすいですね。

今回書いたものはGistにあげています:

-   <https://gist.github.com/hirokiky/5108354>

面倒臭かったのでベタベタに書いていますが、まぁいいでしょう。

ただ1点、後々「いいなー」と思った書き方:

``` {.sourceCode .python}
import sqlalchemy as sa

sa.create_engine(...)
```

これのように `sqlalchemy`
そのままimportしておいて、毎回書いてやることですね。

あとはまぁ、SQL表現言語を、SQLAlchemyのORMと併せて使ってやるのも面白いようです。
なので気が向いたらやってみようと思います。

