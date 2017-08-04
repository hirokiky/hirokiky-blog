Djangoでアップロードしたファイルを非同期に削除する
==================================================

[Django](https://djangoproject.com/) の FileField
を通してアップロードしたファイルを非同期に削除したい。

本質的にはモデルが削除されると同時にファイルも削除したいというものなんだけど、
そこを同期処理しているとやってられないので、非同期で削除しようという話。

案1
---

-   Django
    のシグナルを使って、モデルが削除されたときに非同期でファイルを削除する
    タスクを呼び出す。

私はシグナルにいい思い出がなかったので、この方法はちょっと疎遠したかった。

案2
---

-   カスタムのストレージを書いて、ファイルの削除を非同期に行わせる。

これを採用。

### 残念な思い込み

FileField を持つモデルのインスタンスが delete されるときに、ファイルの
delete も走ると思っていた。 でも実際はそうじゃなくてモデルが delete
されても、ファイルの delete (ストレージの delete)
は走らなかった(まぁ勝手に削除されるのはこわいか)。

明示的に FieldFile インスタンスの delete を呼ぶ必要があった:

``` {.sourceCode .python}
class Test(models.Model):
    file = models.FileField(....)

test.file.delete()  # まぁこんなかんじ。
```

(ここでモデルのインスタンスにある file 属性は
django.db.models.fields.FieldFile のインスタンス で、こいつの delete
がストレージの delete を呼び出す)

書いたもの
----------

-   <https://gist.github.com/hirokiky/5697905>

非同期にファイルを削除するストレージを書いている。

でもまぁ結局 UploadedFile モデルの delete
をオーバーライドして、ファイルも削除するようにしている。
これなら案1のように、 delete
のシグナルでファイル削除用の非同期タスクを走らせても大差なかった。

非同期処理の書き方については自分のブログ記事が役に立った:

-   [django-celeryで非同期処理クイックスタートガイド](http://blog.hirokiky.org/2013/03/23/quick_start_guid_about_django_celery.html)

まとめ
------

残念

