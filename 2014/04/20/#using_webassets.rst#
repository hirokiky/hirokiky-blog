Build, Minify, Merge TypeScript by webassets
============================================

Today, I introduce webassets_ to build TypeScript, minify each generated JavaScript, and merge it. 

webassets_ is a media manager written in Python
to merge JS, compress JS/CSS, uglifying, and so on.
and It also provide the way to coraborate with a veriety of WebFrameworks. 

But in this post, you can learn way to build, minify, merge JavaScript
from commandline interface by webassets_:

* Building TypeScript
* Minifying JavaScript
* Merging JavaScript

Install
-------

First, install webassets_
and I'll write config file for the buliding as YAML file.
so you also need webassets_ with PyYAML. 

::

  pip install webassets
  pip install PyYAML

now you might get webassets==0.9, PyYAML==3.11. 

And to minify JavaScripts, install jsmin.

::

  pip install jsmin

Python requiremests are them.
And ofcause TypeScript is necessary to bulid them. 

::

  npm install -g typescript

now you'd better to check 'tsc' command.

OK. let's make it. 

Configuation for building
-------------------------

Now let's write the configuration file.  You can put 'webassets.yml' file you want. 
In this post, I won't show you how to write it step by step.
But I'll show you a case and it's sample file, and then, describe each meanings of it.

The case is for building a JavaScript file to a web application.
It requires three external libraries (jquery, knockout, and underscore)
and files I wrote are written in TypeScript.
So you need to compile ts files and merge all of them and also minfy it.
The number of TypeScript files is 7, 4 difinition file and 3 application files
The config file of this case will be like this:

.. code-block:: YAML

    directory: ./karmaid/static
    debug: False
    bundles:
      js-karmaid:
        contents:
          - js/lib/jquery-2.1.0.js
          - js/lib/knockout-3.1.0.js
          - js/lib/underscore-1.6.0.js
          - contents:
            - js/app/config.d.ts
            - js/lib/jquery.d.ts
            - js/lib/knockout.d.ts
            - js/lib/underscore.d.ts
            - js/app/api.ts
            - js/app/ko_flushvalue.ts
            - js/karmaid.ts
            filters: typescript
        filters: jsmin
        output: js/karmaid.js

* directory:
    it means the root directory http://webassets.readthedocs.org/en/latest/environment.html#webassets.env.Environment.directory
* debug:
    you can switch it by own enviroments http://webassets.readthedocs.org/en/latest/environment.html#webassets.env.Environment.debug
* bundles:
    you can put own 'bundles' under it. Each bundles are corresponding to each output files.
    so If you want to create JS file for Top page of your application, you may create js_top bundle and write config to merge or minify some Javascript files.
    In this case only 'js-karmaid' is appearing.
* contents:
    the output file will be constructed by each elements of this list.
    Own elements will be automatically merged and the file written in 'output' will be generated. 
    And it can discribe 'nested bundle' in it. In this case, each typescript files are in js-karmaid bundle which means these ts files will be builded before minifying and merging each JavaScript files.
* filters:
    Specify to minify, build, uglify and so on.

    * jsmin is for minifying JavaScript by 'jsmin' https://pypi.python.org/pypi/jsmin
    * typescript is for building TypeScript and generating JavaScript files. If you use external libraries you also need specify difinitely files https://github.com/borisyankov/DefinitelyTyped

    You can learn about all of filters provided by webassets_ http://webassets.readthedocs.org/en/latest/builtin_filters.html

Run command
-----------
After making the config. 
Run the command to build::

  webassets -c webassets.yml build

Then you can get own JavaScript files to correspond to each bundles.
In abeve example, you can get 'karmaid.js' file.

If you want to generate one JS, run it::

  webassets -c webassets.yml build <bundle name>

it's over.

Why webassets
-------------

Because I wanted to try it.
You know, you can choice fanstatic or grunt too. 

And I want to use it with Pyramid and Python3, but now,
pyramid_webassets on PyPI isn't support Python3. 
This supporting will be finished soon
(https://github.com/sontek/pyramid_webassets/issues/23).

.. _webassets: https://pypi.python.org/pypi/webassets/0.9

.. author:: default
.. categories:: none
.. tags:: none
.. comments::
