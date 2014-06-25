---
layout: post
title: "Loading fixtures in Django tests"
date: 2012-02-13 14:22
comments: true
categories: [django, python]
description: "How to load fixtures in Django tests"
keywords: "django, python, testing, fixtures, dry, tdd"
---
When writing tests in Django apps, I have an urge to reuse code in setUp() across all tests (most of my apps use the same setUp). So I come up with a GlobalSetup class that other tests should inherit from (it's located at the project level). However, this approach creates a problem with loading fixtures. According to Django [docs](https://docs.djangoproject.com/en/dev/topics/testing/): <!-- more -->

{% blockquote %}
Once you've created a fixture and placed it in a fixtures directory in one of your INSTALLED_APPS, you can use it in your unit tests by specifying a fixtures class attribute on your django.test.TestCase subclass
{% endblockquote %}

And you would normally do this to load the fixtures:  
``` python
from django.test import TestCase

class PoniesTest(TestCase):
    fixtures = ['ponies']

    def test_ponies(self):
        call_some_test_code()
```
How should we load the fixtures now, when the test file is not at the apps level?
Instead of creating fixures folder and placing fixtures in them (in every app), a better and neater way to handle this would be to put all fixtures in one folder at the project level and load them. You can do this with `call_command` as following:  
``` python
from django.test import TestCase
from django.core.management import call_command

class GlobalSetup(self):
    # Create some ponies and snakes here
    def setUp(self):
        # Load fixtures
        call_command('loaddata', 'tests/ponies', verbosity=0)
```

Invoking this `call_command` is equivalent to running `manage.py loaddata /path/to/fixtures`
The test in each app can now inherit GlocalSetup and reuse setUp():
``` python
from project.tests import GlobalSetup

class PoniesTest(GlobalSetup):
    def setUp(self):
        super(PoniesTest, self).setUp()
    
    def test_ponies(self):
        call_some_test_code()
```
You could also limit each test to load different fixtures by passing a list of fixtures name `['ponies', 'snakes']` to setUp().
Hope this will help you to DRY up your tests.


