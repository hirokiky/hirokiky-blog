DjangoのAUTHORSに追加された
===========================

先日、Djangoに送っていたプルリクエストが取り込まれた。 そんで
[AUTHORS](https://github.com/django/django/blob/master/AUTHORS)
(Djangoに貢献したことがある人が書いてあるテキスト) に追加された。

初めてのことで嬉しいのでブログに書いてます。 あとは Django
のコード書く人増えたらいいなと思って書く。

プルリクエストしたもの
----------------------

HttpResponseRedirect\* に `.url`
という属性を追加するという、小さな新機能です。
この属性は単にレスポンスヘッダの `Location` の値を返すもの。つまりは

``` {.sourceCode .python}
url = property(lambda self: self['Location'])
```

こういうことです。 今まで `response['Location']` と書いていたのが
`response.url` で済むわけです(素敵!)。 そもそもなんで `Location`
なんてヘッダの名前覚えとかないといけないのか、とね。

ちなみに実装上で、この値を頻繁に読むということはないけど、テストでよく使いますね。

-   変更点はこちら [Fixed \#18558 -- Added url property to
    HttpResponseRedirect\*](https://github.com/django/django/commit/e94f405d9499d310ef58b7409a98759a5f5512b0)
-   チケットはこちら [Supply url property to HttpResponseRedirect and
    HttpResponsePermanentRedirect](https://code.djangoproject.com/ticket/18558)

報告者の coolRR さん、取り込んでくれた claudep
さんありがとうございます。

Djangoを読み書きしよう
----------------------

上記の対応が取り込まれて、はれてDjangoの
[AUTHORS](https://github.com/django/django/blob/master/AUTHORS#L309)
に追加されました。日本人では4人目ですかね。

4人しかいないのはちょっと寂しいので、Django使ったことあるよという方はぜひパッチ/プルリクエストを
送ってみましょう。
Djangoのソースコードを読んで書くのはそれなりに楽しいと思う。

私はHTTP Handlingなんかが好きで読んでます。 `django.core.handlers.base`
などを読んでみると
処理の流れが見えて面白い。あとはWSGIについて知ってみて、そこから
`django.core.handlers.wsgi` を読めばもっと良いと思う。

そういうと2月23日に世界3箇所で DjangoSprint が開催されます。
[日本でも便乗してやります](http://connpass.com/event/1850/) 。
ぜひこの機会にDjangoのコードを読んでみて書いてみましょう。

私はそこでもDjangoのチケットに対応するなりをしたいと思ってる。

ではまたそのときに。

