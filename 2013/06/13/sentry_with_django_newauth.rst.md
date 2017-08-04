Sentry with django-newauth
==========================

[djangonnewauth](http://ianlewis.bitbucket.org/django-newauth/) is a
library to add customizable user model (It developed before Django 1.5).
And [sentry](https://github.com/getsentry/sentry) is a platform to
collect logs and aggregate these.

[Sentry](https://github.com/getsentry/sentry) can collect errors raised
by some django applications, and displays informations of these errors.

Problem
-------

[Sentry](https://github.com/getsentry/sentry) can tell us a user
encountered some errors. But, if the application uses
[djangonnewauth](http://ianlewis.bitbucket.org/django-newauth/), the
user provided by newauth will not be displayed.

I tried to write a SentryPlugin to display the newauth's user
information. As it turns out, I could not do it. It was a difficult than
I thought.

Solution
--------

I wrote a package to solve this
[raven\_django\_newauth](https://pypi.python.org/pypi/raven-django-newauth)
.

You can set SENTRY\_CLIENT =
'raven\_django\_newauth.client.DjangoNewauthClient', and then, User
interface of Sentry provides a information of newauth's user.

Sentry and Raven
----------------

[Sentry](https://github.com/getsentry/sentry) collects informations
sended by [raven](https://github.com/getsentry/raven-python).
[Sentry](https://github.com/getsentry/sentry) defines general interfaces
(for example a User), and
[raven](https://github.com/getsentry/raven-python) traslates collected
data to thing in consideration of these interfaces. A client send
dictionary thats key is path to each interfaces (like
'setry.interfaces.User).

The User interfaces in sentry.interfaces.User. And on django
application, the [raven](https://github.com/getsentry/raven-python)
client is raven.contrib.django.client.DjnagoClient. The client gets user
information from get\_user\_info method and stores to the dictionary as
a key 'sentry.interfaces.User'

