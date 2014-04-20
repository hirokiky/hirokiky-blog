Troubleshooting: Geodjango's HAS_GDAL is False in OSX
=====================================================

Troubleshooting guide when django.contrib.gis.gdal.HAS_GDAL is False.

Environment
-----------
- Mac OSX Lion
- GDAL 1.10 (by using `Kyng Chaos's package <http://www.kyngchaos.com/software/frameworks>`_.
- Django 1.5
- Python 2.7

osgeo.gdal
----------
First, I realized the python can't use the gdal in my virtualenv.
Fixed it, refferencing this article.

- http://linfiniti.com/2013/02/installing-python-gdal-into-a-python-virtualenv-in-osx/

Then, the gdal can be imported. but, HAS_GDAL was still False.

Specifying GDAL library
-----------------------
I noticed the django did'nt know the GDAL library path.
It expected in '/user/local/lib/libgdal.dylib'.
but, in my environment, It is in '/Library/Frameworks/GDAL.framework/unix/lib/libgdal.dylib'.

To tell this path, we can set the GDAL_LIBRARY_PATH settings to django.

- GDAL_LIBRARY_PATH = '/Library/Frameworks/GDAL.framework/unix/lib/libgdal.dylib'

After this setting, gdal.HAS_GDAL will be True.
You should referrence this documentation too.

- https://docs.djangoproject.com/en/dev/ref/contrib/gis/install/geolibs/#installing-geospatial-libraries

.. author:: default
.. categories:: none
.. tags:: django,geodjango,gdal
.. comments::
