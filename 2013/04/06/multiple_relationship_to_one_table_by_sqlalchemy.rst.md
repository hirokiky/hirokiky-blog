SQLAlchemyで1つのテーブルに複数種のリレーションを貼る
=====================================================

ある1つのテーブルに複数種のリレーションを貼りたい。

前提
----

「映画」と「言語」を対象に考えてみると、ある映画と言語には2つの関係が持たせられる:

1)  その映画がどの言語を対象にしたものか
2)  その映画は元々どの言語のものなのか

例えば日本語吹き替え版「バックトゥザフューチャー」は、対象言語が日本語で、
元言語が英語ということになる。

これについてのテーブルを考えて、FilmとLanguageという2つを用意する。
Filmには対象言語へのFKであるlanguage\_idと、元言語へのFKであるoriginal\_language\_id
の2つを持たせてやる。もちろんFKの対象はLanguageテーブルである。

こういったテーブルがある場合に [SQLAlchemy](http://www.sqlalchemy.org/)
でどうやって対応するのかという話。

ちなみに議題にあげたテーブルはMySQLの
[Sakila](http://dev.mysql.com/doc/sakila/en/index.html) という
サンプル用データベースの、Film、Languageテーブルのこと。

バージョン
----------

-   SQLAlchemy==0.8.0

書く
----

とりあえずこんなかんじ。nameとかのカラムは下には転記してない。

``` {.sourceCode .python}
mport sqlalchemy as sa
from sqlalchemy.dialects import mysql
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import relationship

Base = declarative_base()

class Film(Base):
    __tablename__ = 'film'
    film_id = sa.Column(sa.SmallInteger(unsigned=True),

                        primary_key=True, nullable=False)

    language_id = sa.Column(mysql.TINYINT(unsigned=True),
                            sa.ForeignKey("language.language_id"),
                            nullable=False)
    original_language_id = sa.Column(mysql.TINYINT(unsigned=True),
                                     sa.ForeignKey("language.language_id"))

    language = relationship(
        'Language',
        primaryjoin="Film.language_id==Language.language_id")
    original_language = relationship(
        'Language',
        primaryjoin="Film.original_language_id==Language.language_id")


class Language(Base):
    __tablename__ = 'language'
    language_id = sa.Column(mysql.TINYINT(unsigned=True),
                            nullable=False,
                            primary_key=True)
```

relationshipしてあげるわけですが、ここでprimaryjoinを指定してあげないと
いけなかった。そうしないとlanguage\_idとoriginal\_language\_idのどっちを使って
Languageをとってくればいいか分からない。
language、original\_languageの2つを用意してあげて、それぞれどういう条件で取ってくる
かをprimaryjoinで教えてあげる。

あるけみすとへの道は遠い

