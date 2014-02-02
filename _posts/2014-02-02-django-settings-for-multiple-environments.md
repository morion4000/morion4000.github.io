---
layout: post
title: Django settings for multiple environments
description: "Create and configure multiple Django settings files for multiple environments."
modified: 2014-02-02
tags: [django, settings, local, heroku]
image:
  feature: abstract-3.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
comments: true
share: true
---


When developing a Django application you will most likely need to have specific settings for each environment. It's either you have complex deployment stages or just a local and a production environment you will have to have a good way to divide the settings.

There are several ways of achieving the end results, however my favorite one is having separate settings files.

### Split `settings.py` into several files
The common Django 1.6 directory structure looks a lot like this:

{% highlight bash %}
- project/
   - manage.py
   - requirements.txt
   - project/
      - __init__.py
      - urls.py
      - wsgi.py
      - settings.py
   - app1/
   - app2/
{% endhighlight %}

You need to create a `settings` directory inside `project/project`. In the newly created directory, you need to create a `__init__.py` file so that the python module can be included.

Creating `base.py` is desirable for declaring settings that are environment agnostic.

In this example we have a development and a production environment. Create `local.py` for development and `heroku.py` for production settings.

The new directory structure for the Django application looks like this:

{% highlight bash %}
- project/
   - manage.py
   - requirements.txt
   - project/
      - __init__.py
      - urls.py
      - wsgi.py
      - settings/
      	  - __init__.py
          - base.py
          - local.py
          - heroku.py
   - app1/
   - app2/
{% endhighlight %}

### Move the settings across the newly created files

Having the settings divided it's a matter of preference and necesity. I usually have the database credentials set for each environment.

Add the import for the base settings in each of the different env files:

{% highlight python %}
from base import *
{% endhighlight %}

For detailed Heroku settings for Django check [https://devcenter.heroku.com/articles/getting-started-with-django#django-settings](https://devcenter.heroku.com/articles/getting-started-with-django#django-settings).

### Set the settings path

Locally there are several ways of setting the path for the settings module. You can either set the environment variable `DJANGO_SETTINGS_MODULE`:

{% highlight bash %}
export DJANGO_SETTINGS_MODULE=project.settings.local
{% endhighlight %}

or assign the `--settings` flag when starting the development server:

{% highlight bash %}
python manage.py runserver --settings=project.setttings.local
{% endhighlight %}

On production environments it's pretty much the same. The only thing that changes is the level of access you have. On Heroku for example you can not connect via SSH or have a configuration file for environment variables, however the command line tool allows you to set environment variables:

{% highlight bash %}
heroku config:set DJANGO_SETTINGS_MODULE=projects.settings.heroku
{% endhighlight %}

### Remove the default assignment for DJANGO_SETTINGS_MODULE

The `DJANGO_SETTINGS_MODULE` setting is overwritten in `wsgi.py`. Open `project/project/wsgi.py` and remove the lines that assign a default value:

{% highlight python %}
...
import os
os.environ.setdefault("DJANGO_SETTINGS_MODULE", "project.settings")
...
{% endhighlight %}