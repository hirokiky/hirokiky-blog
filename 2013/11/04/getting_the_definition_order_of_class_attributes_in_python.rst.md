Getting the definition order of class attributes in python
==========================================================

Same question was in StackOverFllow, but the answer was not so soft on
me.

-   <http://stackoverflow.com/questions/4220747/how-do-i-get-the-definition-order-of-class-attributes-in-python>

It gave me a some hints, but it actually says 'Watch a code'. Ok, so I
read the code and understood that behavior, I will write that
description here.

Why is definition order necessary
---------------------------------

The best example is on Form libraries. The definition order will affect
to rendering order directory.

Consider on a suprious form library, like this:

``` {.sourceCode .python}
class MyForm(Form):
    name = StringField()
    text = StringField()
```

and it should be rendered that the first place is name and the second
text like this oreder:

``` {.sourceCode .html}
<input value='name' />
<input value='text' />
```

Of cause, general form libraries consider the definition order, such as
Djnago Form, deform (actually, colander's behavior).

On my case, I should handle it on creating [Uiro
framework](https://pypi.python.org/pypi/uiro), definition views in
controller.

Answer
------

To handle the order, you should place a counter. The counter will be
increased on each constraction of orderd attributes. And then, each
attributes stores that counter value to it's own.

``` {.sourceCode .python}
>>> import itertools
>>> class Field(object):
...     _counter = itertools.count()
...     def __init__(self):
...         self._order = next(Field._counter)
... 
>>> class Form(object):
...     name = Field()
...     text = Field()
... 
>>> Form.name
<__main__.Field object at 0x7f0abfb26250>
>>> Form.name._order
0
>>> Form.text._order
1
```

Ok, seems good. Then, apply this practice on writing metaclass:

``` {.sourceCode .python}
>>> class FormMetaClass(type):
...     def __new__(cls, name, base, attrs):
...         new_class = super(FormMetaClass, cls).__new__(cls, name, base, attrs)
...
...         fields = [(name, value) for name, value in attrs.items()
...                   if isinstance(value, Field)]
...
...         # sorting manually corresponds to the definision order of Fields.
...         fields.sort(key=lambda e: e[1]._order)
...
...         new_class.fields = fields
...         return new_class
... 
>>> class Form(metaclass=FormMetaClass):
...     name = Field()
...     text = Field()
... 
>>> Form.fields[0]
('name', <__main__.Field object at 0x7f0abfb26410>)
>>> Form.fields[1]
('text', <__main__.Field object at 0x7f0abfb26490>)
```

sweet, the definition order didn't lost. In \_\_new\_\_ method, the
**fields** before sorted, the order of Fields is not considered in
**fields**, because the **attrs** is just a dictionary. so we should
apply **\_oreder** attribute to Field and re-consider the order manually
using that **\_order**.

I used this tips on this change, check it out.

-   <https://github.com/hirokiky/uiro/commit/d7a840c09ce30af10dc81fd86fbbf21772910833>

