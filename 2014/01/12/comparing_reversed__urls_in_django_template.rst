Comparing reversed URLs in Django template
===========================================

In Django template we can use 'url' template tags to
get path to a view, like this:

.. code-block:: django

    {% url 'blog:top' %}

It may render a '/blog/' text.

How can we compare this returned path?
You can use 'as' syntax on url template tag to save the returned value to a veriable,
and compare it with some string or other variables:

.. code-block:: django

   {% url 'blog:top' as top_path %}

   compareing with string.
   {% if top_path == '/blog/'%}The path to blog top was '/blog/'{% endif %}

   with other variables.
   {% if top_path == request.path %}You are at the top of my blog{% endif %}

But generally, this tip is not so smart way.
Because templates should not have many logics and know about others.

Let's consider some anti-pattern for this tip.
If you want not to render some text if a user is in the specific page,
you can write this template:

.. code-block:: django

    {# In base.html #}
    <html>
    <body>
    <a href="{% url 'top' as top_path %}
             {% if top_path != request.path %}
                 {{ top_path }}
             {% endblock %}">Bland Logo</a>
    {% block content %}{% endblock %}
    </body>
    </html>

.. code-block:: django

    {# In top.html #}
    {% extends 'base.html' %}
    {% block content %}
        Hello This is top page.
    {% endblock %}

This 'if' syntax in base.html means "Don't render the path for top page if a user is in the top page".
It might work correctly. But it can be relized by more simply way, using a block syntax:

.. code-block:: django

    {# In base.html #}
    <html>
    <body>
    <a href="{% block bland_path%}{{ top_path }}{% endblock %}">Bland Logo</a>
    {% block content %}{% endblock %}
    </body>
    </html>

.. code-block:: django

    {# In top.html #}
    {% extends 'base.html' %}
    {% block top_path %}{% endblock %}{# Overriding with empty text #}
    {% block content %}
        Hello This is top page.
    {% endblock %}

That's it. In this, the child template know about the behavior for hiding the link for top page.
In previous one, the template should access 'request', so you need to use 'contextprocessors.request'.
And it is painfull to add page not to render the link.
So If you want to compare urls in django template, you might re-consider and notice it can be realised by
inheritance.

.. author:: default
.. categories:: none
.. tags:: django
.. comments::
