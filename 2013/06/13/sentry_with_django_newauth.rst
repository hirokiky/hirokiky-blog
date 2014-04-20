Sentry with django-newauth
==========================
django-newauth_ is a library to add customizable user model (It developed before Django 1.5).
And sentry_ is a platform to collect logs and aggregate these.

Sentry_ can collect errors raised by some django applications, and displays informations
of these errors.

Problem
-------
Sentry_ can tell us a user encountered some errors.
But, if the application uses django-newauth_, the user provided by newauth will not be displayed.

I tried to write a SentryPlugin to display the newauth's user information.
As it turns out, I could not do it. It was a difficult than I thought.

Solution
--------
I wrote a package to solve this
`raven_django_newauth <https://pypi.python.org/pypi/raven-django-newauth>`_ .

You can set SENTRY_CLIENT = 'raven_django_newauth.client.DjangoNewauthClient',
and then, User interface of Sentry provides a information of newauth's user.

Sentry and Raven
----------------
Sentry_ collects informations sended by raven_.
Sentry_ defines general interfaces (for example a User),
and raven_ traslates collected data to thing in consideration of these interfaces.
A client send dictionary thats key is path to each interfaces (like 'setry.interfaces.User).

The User interfaces in sentry.interfaces.User.
And on django application, the raven_ client is raven.contrib.django.client.DjnagoClient.
The client gets user information from get_user_info method and stores to the dictionary as 
a key 'sentry.interfaces.User'

.. _django-newauth: http://ianlewis.bitbucket.org/django-newauth/
.. _sentry: https://github.com/getsentry/sentry
.. _raven: https://github.com/getsentry/raven-python

.. author:: default
.. categories:: none
.. tags:: django,sentry,django-newauth
.. comments::
