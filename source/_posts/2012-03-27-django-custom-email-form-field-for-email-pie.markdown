---
layout: post
title: "Django custom email form field for Email Pie"
date: 2012-03-27 01:14
comments: true
categories: [python, django]
description: "Create Django EmailPieField"
keywords: "python, django, form, field, email, formfield, emailfield, custom"
---
{% pullquote %}
As you know Django's `forms.EmailField()` is capable of validating email addresses but what if you wanted to validate the existence of a particular email address? I've found a pretty neat solution for that, it's called [Email Pie](http://emailpie.com/). 
{"Email Pie is a wonderful little JSON API that gives you a simple way to validate email addresses. We'll let you know if that email address is legit or not."}. You could either use the service hosted on emailpie.com or run your own instance (it's open source). In this post, I'm going to show you how to write a custom form field that makes use of Email Pie.
<!-- more -->
Make sure you pip install `requests` and `simplejson` before proceeding. Now create a `fields.py` file with the following content:

``` python fields.py
import requests
import simplejson

from django import forms
from django.core.exceptions import ValidationError


EMAIL_PIE_HOST = 'http://emailpie.com'  # change this if you run your own instance

def validate_email_pie(value):
    params = {'email': value}
    response = requests.get('%s/v1/check' % EMAIL_PIE_HOST, params=params)
    response = simplejson.loads(response.content)

    if not response['success']:
        raise ValidationError(response['errors'][0]['message'])


class EmailPieField(forms.EmailField):
    default_validators = [validate_email_pie]
```

`validate_email_pie` method makes GET request to Email Pie and gets back a response. The service checks for email format and mx records, it also catches misspellings (you can get it with `response['didyoumean']`). `validate_email_pie` raises `ValidationError` if the service detects an invalid email address. Now in your forms you can use `EmailPieField` like this:

``` python forms.py
from django import forms
from .fields import EmailPieField

class ContactForm(forms.Form):
    name = forms.CharField()
    email = EmailPieField()
```

For those interested, here are some tests for the field :)

``` python tests.py
from django.test import TestCase
from django.core.exceptions import ValidationError
from .fields import EmailPieField


class EmailPieFieldTest(TestCase):

    def test_invalid_email(self):
        email_field = EmailPieField()
        self.assertRaises(ValidationError, email_field.clean, 'email@totest')

    def test_invalid_mx(self):
        email_field = EmailPieField()
        self.assertRaises(ValidationError, email_field.clean, 'email@totest.com')

    def test_valid_email(self):
        email_field = EmailPieField()
        self.assertEquals(email_field.clean('nam@namis.me'), 'nam@namis.me')
```
{% endpullquote %}