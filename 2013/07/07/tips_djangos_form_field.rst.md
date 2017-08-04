Tips: for Initial arg of Django's Form fields
=============================================

Django's Form fields take a `initial` argument to specify the initial
value for that Field.

For more detail about `initial`, check out the doc [Form fields
\#initial](https://docs.djangoproject.com/en/dev/ref/forms/fields/#initial)

Basically...
------------

As written there, if you want to set a initial value as a result of a
callable, you should pass the callable directly to the argument. like
this:

``` {.sourceCode .python}
class DateTimeForm(forms.Form):
     now = forms.DateTimeField(initial=datetime.datetime.now)
```

With arguments
--------------

### Mistake

Let's consider the case passing some argument to callable to pass to
initial. A common mistake is like this:

``` {.sourceCode .python}
# Don't do this
class DateTimeForm(forms.Form):
     now = forms.DateTimeField(initial=datetime.datetime.now(tz))
```

In this case, the now value would have been fixed at the time you start
the server. You should pass a callabse directary, how do you pass
arguments to the callable?

### Elegant solution

Let's put together the calling with partial function application. The
following is a simple example using `functools.partial`.

``` {.sourceCode .python}
>>> from functools import partial
>>>
>>> zero_until_ten = partial(range, 10)
>>> zero_until_ten()
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

Yes, you made a callable without arguments. This technique can be used
in the previous example, like this:

``` {.sourceCode .python}
class DateTimeForm(forms.Form):
     now = forms.DateTimeField(initial=partial(datetime.datetime.now, tz))
```

The `initial` will be passed a callable, so the resulting value will not
be fixed.

It's beautiful.

### Inelegant solution

Alternatively, you can write like this:

``` {.sourceCode .python}
class DateTimeForm(forms.Form):
     now = forms.DateTimeField()

    def __init__(self, *args, **kwargs):
        super(DateTimeForm, self).__init__(*args, **kwargs)
        self.fields.get('now')._set_choices(datetime.datetime.now(tz))
```

The beauty declarative was lost.

Another develoaper must read `__init__`, and you should be careful not
to forget the calling `super`.

Not recommended. Use the `functools.partial` function.

