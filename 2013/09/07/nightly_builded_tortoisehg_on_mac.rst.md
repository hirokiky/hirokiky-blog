Mac & Mercurialユーザーにオススメ､TortoiseHgのナイトリービルド版があるよ
========================================================================

熱心なMercurialユーザーに支持を集めるTortoiseHgですが､
そのTortoiseHgのMac向けのビルド版がダウンロードできるようです｡

-   <https://s3.amazonaws.com/kiln-tortoisehg-nightlies/index.htm>

ここでは簡単な紹介と､Non-ASCII件で起こる問題の解決方法を書きます｡

TortoiseHgとは
--------------

MercurialのGUIクライアントです｡
Git難しいDVCS難しいという方にこそオススメしたいMercurialですが､
そのGUIクライアントのなかでも優秀と評判なのがTortoiseHgです｡

私個人的に､SourceTreeなどと比べて､TortoiseHgが素晴らしいのは Mercurial
Queue(MQ)対応です｡Mercurialの拡張機能への対応も充実しているのは
とてもありがたいです｡というのも私は普段MQも使っているので､MQなしではやってら
れなくなっちゃいます｡

導入手順
--------

導入手順といってもビルド版なので基本はダウンロードするだけです｡

-   <https://s3.amazonaws.com/kiln-tortoisehg-nightlies/index.htm>

これをダウンロードして解凍して起動するだけです｡

Thanks Kevin!!11!!

日本語問題対応手順
------------------

ですがこのままでは日本語で文字化けしてしまいます｡
以下のようにして解決しましょう

### 環境変数設定

Mercurialで使用する文字コードを決めるには環境変数にHGENCODING=utf-8を設定をすればよいです｡

ですがただ
**ターミナルから設定してもMacの各アプリケーションには有効にならないので**
グローバルな値として設定しましょう｡

まずは以下のファイルを管理者権限で作成します｡

-   /etc/launchd.conf

そしてそこに､以下のように環境変数の設定を書いてやります｡

-   setenv HGENCODING utf-8

あとは再起動するだけです｡

-   [Mountain
    Lionでのシステムワイド環境変数の設定方法](http://qiita.com/jjzak/items/bb6a03538d4ee773c9b6)

この記事に助けられました(SnowLeopardでしたがうまくいきました)

Mercurialユーザーの春がきた

> **note**
>
> この手順を適応すれば例えば Mac + Pycharm(Python向けのIDE) +
> Mercurialの連携時に
> 発生する日本語問題にも対応できます(Pycharmからannotateしたら文字化けしたりしますよね)｡

余談:Twitterにて
----------------

日本語問題について

あざすあざす

