Django's TestCase.multi_db attribute is mistake
===============================================

Django's test framework (django.test.TestCase) has a atribute
`multi_db <https://docs.djangoproject.com/en/dev/topics/testing/overview/#multi-database-support>`_ .
It should be set True when testing on multiple databases (False, by default).

If you forget this setting, and your test uses multiple databases,
the test suite only flushes the 'default' database
(more exact django.db.utils.DEFAULT_DB_ALIAS) without flushing another databases.

And then, some trash datas will left on these databases (ignore the 'default' database).
After tests will have risk of failing in absurd reason.
I'm handling multiple databases on my work, and every time I forget setting this.
And the mistake is difficlut to notice. Of cause, there are no errors.
This is **expected** behavior.

This behavior (only flushing 'default') is feature for speeding up.
Because, flushing a database is slow, and it will be run for each tests.

But, I think, it should be flush all databases by default.

If your application using multiple databases, but a TestCase use only 'default',
then you can set 'flush_only_default = True' (for example)
to force a test suite flushing only 'default' database.

Of cause, your application uses only 'default' database,
only 'default' will be flushed even if without setting flush_only_default = True.

I think the behabior for speeding up is optional.
By default, it should be performed as indubitable even if it will be slow.

The change for this will be like this:

.. code-block:: diff

    diff --git a/django/test/testcases.py b/django/test/testcases.py
    index a9fcc2b..0558476 100644
    --- a/django/test/testcases.py
    +++ b/django/test/testcases.py
    @@ -466,11 +466,11 @@ class TransactionTestCase(SimpleTestCase):
         def _databases_names(self, include_mirrors=True):
             # If the test case has a multi_db=True flag, act on all databases,
             # including mirrors or not. Otherwise, just on the default DB.
    -        if getattr(self, 'multi_db', False):
    +        if getattr(self, 'flush_only_default', False):
    +            return [DEFAULT_DB_ALIAS]
    +        else:
                 return [alias for alias in connections
                         if include_mirrors or not connections[alias].settings_dict[
    -        else:
    -            return [DEFAULT_DB_ALIAS]
     
         def _reset_sequences(self, db_name):
             conn = connections[db_name]

I asked about this proposal on django developers IRC channel (#django-dev on freenode).
Some people answered me (Thanks a lot!), in side disagree.
Certainly, this proposal is not have great gain. and having a big risk breaking
compabitity.
I noticed this is not good proposal, but I still claim the multi_db behavior is mistake.

Yes, I will set multi_db = True on a base class, and subclassing it on own tests.

.. author:: default
.. categories:: none
.. tags:: django,misc
.. comments::
