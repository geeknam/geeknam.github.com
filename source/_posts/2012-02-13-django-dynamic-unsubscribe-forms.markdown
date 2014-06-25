---
layout: post
title: "Django dynamic unsubscribe forms"
date: 2012-02-13 15:40
comments: true
categories: [django, python]
description: "A detailed view on dynamic forms in Django"
keywords: "django, python, forms, dynamic"
---
The normal usecase of Django forms is to have predefined fields in forms like this:

``` python
from django import forms

class EmailPreferenceForm(forms.Form):
    daily_digest = forms.BooleanField(required=False)
    weekly_news = forms.BooleanField(required=False)
    monthly_summary = forms.BooleanField(required=False)
```

What if we wanted to add more fields to the form like `weekly_digest` and so on? Well, of course you would have to add a new field to the form and maybe handle extra work in your views. Let's make this more dynamic.
<!-- more -->
Let's create Event model which will store all events. Entries from this model will be generated into fields in the form later:

``` python
from django.db import models

class Event(models.Model):

    title = models.CharField(
        max_length=100,
        help_text='e.g: Weekly News'
    )
    title_id = models.CharField(
        max_length=100,
        blank=True,
        editable=False
    )

    def __unicode__(self):
        return self.title

    def save(self, *args, **kwargs):
        if not self.title_id:
            # Generate title_id based on title
            # Weekly News > weekly_news
            self.title_id = self.title.lower().replace(' ', '_').replace("'", '')
```

And now the form that will generate fields based on all entries in Event model

``` python
from django import forms
from models import Event

class EmailPreferenceForm(forms.Form):
    # Auto generate fields based on title_id in Event
    def __init__(self, *args, **kwargs):
        super(EmailPreferenceForm, self).__init__(*args, **kwargs)
        fields = Event.objects.all()
        for f in fields:
            self.fields[f.title_id] = forms.BooleanField(required=False)
```

Let's say you have a Subscriber model that has many Events. This piece of code will initialize the checkboxes based on subscribers' preferences.

``` python
from forms import EmailPreferenceForm

def email_preferences(request):
    if request.method == 'POST':
        email_preferences = EmailPreferenceForm(request.POST)
        # Process the form
    else:
        # Initialize the preferences. This will make the boxes checked.
        initial = {}
        for e in subscriber.events.all():
            initial[e.title_id] = True
        email_preferences = EmailPreferenceForm(initial=initial)
    
    # Context and HTTPResponses here
```