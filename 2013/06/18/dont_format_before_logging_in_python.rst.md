Don't format strings before logging in python
=============================================

You should not provide formatted string to loggers in Python, like this:

``` {.sourceCode .python}
logger.info('Logged in: %s' % username)
```

You should write like this:

``` {.sourceCode .python}
logger.info('Logged in: %s', username)
```

-   [logging](http://docs.python.org/3.3/library/logging.html)

Why?
----

In mont cases, it is the same as a result. But, internally, the later
one contains more information which part of this string represents a
username.

You shoud realize the first argument (message) can also be used as a
signature.

If you want to aggregate logs, you will group logs by messages, like
this:

-   message: **'Logged in: %s'**, args: ('Ritsu Tainaka',)
-   message: **'Logged in: %s'**, args: ('Mio Akiyama',)

Yes, you will be able to group these logs. They are same log, just
username is different.

OK, now consider this grouping with formatted strings, it will be not
work:

-   message: **'Logged in: Ritsu Tainaka'**, args: ()
-   message: **'Logged in: Mio Akiyama'**, args: ()

They will be handled as different. Of cause, these messages are totally
different.

Practical
---------

[Sentry](https://github.com/getsentry/sentry), error logging and
aggregation platform, it displays logs grouping by these messages.

So, If you use Sentry, you should provide **not formatted** message to
any loggers. Without this, all logs containing some variables will be
handled as different. Yes, as thousands of different logs.

