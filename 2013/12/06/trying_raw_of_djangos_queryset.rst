Using .raw method of Django's QuerySet
==========================================

Today, I show you to use `.raw` method of Django's QuerySet.
The method is powerful and it can manipurate data in DB even if
a column is not appear in Model's field.

Of cause you can choice to use executing raw query by db connection
directory.
But, using raw method of QuerySet is more comfy.
The method allow you to map the values in DB to the objects manually.

Ok, now let me show you a example.
Consider a model calss, named Post, like this:

.. code-block:: python

    class Post(models.Model):
        title = models.CharField(max_length=255)
        body = models.CharField(max_length=255)

The model is simple and common what in like everyday we write.
And then, try to change the schema of it directory::

    sqlite> ALTER TABLE demoapp_post ADD COLUMN "slug" varchar(255);
    sqlite> .schema demoapp_post
    CREATE TABLE "demoapp_post" (
        "id" integer NOT NULL PRIMARY KEY,
        "title" varchar(255) NOT NULL,
        "body" varchar(255) NOT NULL,
        "slug" varchar(255));

just now, I added a column named 'slug'.
The column and model fields have a difference now.
This 'slug' appears in sqlite db column, but not in the model.

And try to insert a row::

    sqlite> INSERT INTO demoapp_post ("title", "body", "slug") VALUES ("test", "test body", "test slug");
    sqlite> SELECT * FROM demoapp_post;
    1|test|test body|test slug

Then let's consider how we can get the value of slug ('test slug').

Use .raw method
------------------

The easiest way is 'adding a field to the model and migrating the existed DB'.
Yes, I know. but, sometime we should get a value from DB even if the column
is not in the model fields.

Then, you might want to use raw method?

We can write a SQL directory as a argument to raw method.
and the return value of the SQL will be mapped to a Model
called the raw method, like this:

.. code-block:: python

    >>> post = Post.objects.raw("SELECT * FROM demoapp_post")[0]
    >>> post.title
    u'test'
    >>> post.body
    u'test body'
    >>> post.slug
    u'test slug'
    >>> post.nothing
    Traceback (most recent call last):
     File "<console>", line 1, in <module>
    AttributeError: 'Post' object has no attribute 'nothing'

Yeah! the value of 'slug' was showed up.
In common way, we can't get the vaule..:

.. code-block:: python
    
    >>> post = Post.objects.all()[0]
    >>> post.title
    u'test'
    >>> post.body
    u'test body'
    >>> post.slug
    Traceback (most recent call last):
     File "<console>", line 1, in <module>
    AttributeError: 'Post' object has no attribute 'slug'

Mapping the attribute more flexible
----------------------------------------
And then, by specifying the 'translations' attribute, we can change
the mapping from DB value to Model attribute.

Ok next, let's try to get the value of 'slug' column, through 'urlslug'
attribute of a model:

.. code-block:: python

    >>> post = Post.objects.raw("SELECT * FROM demoapp_post", translations={'slug': 'urlslug'})[0]
    >>> post.slug
    Traceback (most recent call last):
      File "<console>", line 1, in <module>
    AttributeError: 'Post' object has no attribute 'slug'
    >>> post.urlslug
    u'test slug'

aw, it's spookey, though.

(I checked above codes with Django 1.6)


.. author:: default
.. categories:: none
.. tags:: django,raw
.. comments::
