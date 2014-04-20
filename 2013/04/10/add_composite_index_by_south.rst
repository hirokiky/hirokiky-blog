Southで複合インデックスを貼る
=============================

Southで複合インデックスを貼る。
複合インデックスを使いたい場合、Djangoだと1.5からしか対応してない。

- https://docs.djangoproject.com/en/dev/ref/models/options/#index-together

でも複合インデックスを手動で貼るのはツライ。
そこでこの記事では

- Southから複合インデックスを貼る方法

あと

- Southで吐いたインデックス名を取得する方法

を書く。


名前を取得する理由としては
「生クエリを吐かせて、そこでインデックスのヒントとして与えたい」なんていうときに
使う。

この複合インデックスを貼る機能はSouthの0.4から使えるそう。
今回はDjango1.3、South0.7で動作確認している。

Southで複合インデックスを貼る
-----------------------------
自前でmigrationファイルを書いてやる。
`0001_add_idx_table_name_column1_column2.py` とか名前つけてあげて
`app/migrations/` 以下においてあげるだけ。

``create_index`` を使えばできる。

.. code-block:: python

    from south.db import db
    from south.v2 import SchemaMigration

    class Migration(SchemaMigration):

        def forwards(self, orm):
            db.create_index('table_name', ['column1', 'column2'])

        def backwards(self, orm):
            db.delete_index('table_name', ['column1', 'column2'])

まぁそれ以上でも以下でもない感じ。テーブルとカラムを指定してあげるだけ。
もちろん単一カラムへのインデックスも貼れるよう。

これでマイグレーションすると 'column1', 'column2' への複合インデックスが
貼られる。

- http://south.aeracode.org/wiki/db.create_index

手で書くなら

.. code-block:: sql

    ALTER TABLE table_name ADD INDEX idx_table_name_column1_column2 (column1, column2);

って感じかな。

で、インデックスヒントとか使ってやりたい場合にはインデックスの名前が必要なわけだけど、
Southにインデックス貼らせるとインデックスの名前が自動で生成される。
この名前を取ってやりたい。

Southが貼ったインデックスの名前をとる
-------------------------------------
意外と簡単。

.. code-block:: python

    from south.db import db

    index_name = db.create_index_name('table_name', ['column1', 'column2'])

``create_index_name`` にインデックス作ったときと同じ条件を渡してやるだけ。
まぁ `constracts.py` にでも書いておくのがいいかな。

ただドキュメントに書いてないからソースコード読んでみたほうがいい。

ちょっとインデックス名指定して作れたらいいのに、と思ったけどそれほど需要もなさそう。
まぁ取れるならいい。

.. author:: default
.. categories:: none
.. tags:: south,django,sql
.. comments::
