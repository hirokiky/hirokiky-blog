How to disable the notice of copyright on tinkerer
==================================================

This blog is publishing on CC BY-SA. So, displaying the notice of copyright is not suitable. 

By default, the notice is on footer. But, disabling it is able by adding line to conf.py like this.

.. code-block:: python

   html_show_copyright = False

Also my blog's conf.py is using this way `like this <https://bitbucket.org/hirokiky/hirokikys-blog/src/7d5cb3130b9de3fc373ffc274f1082e67b5afcbc/conf.py#cl-20>`_

How it works
------------
You can find how it's value works on ``tinkerer/themes/boilerplate/layout.html``.

.. code-block:: html

   {%- if show_copyright -%}
      {% trans copyright=copyright|e -%}&copy; Copyright {{ copyright }}. {% endtrans -%}
   {%- endif -%}

It seems that ``html_show_copyright`` on conf.py will be passed to html like shape ``show_copyright``.

Finally, my blog never display the notice of copyright. Check the footer.

.. author:: default
.. categories:: none
.. tags:: tinkerer
.. comments::
