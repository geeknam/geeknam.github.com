---
layout: post
title: "More on Django FormView"
date: 2012-02-13 21:49
comments: true
categories: [django, python]
description: "Django FormView in details"
keywords: "django, python, formview"
---
{% pullquote %}
I've been trying to limit myself to use class based generic views as much as possible. {"Django’s generic views were developed to take away the monotony of repeating certain patterns again and again"}. Some generics views are properly documented (like ListView and DetailView), however I wanted to make use of FormView to process my forms. Unfortunately, there were no FormView examples provided. So here it is.
<!-- more -->

``` python
from django.views import generic

class EmailPreferenceView(generic.FormView):
    form_class = EmailPreferenceForm

    def get(self, *args, **kwargs):
        # You can access url variables from kwargs
        # url: /email_preferences/geeknam > kwargs['username'] = 'geeknam'
        # Assign to self.subscriber to be used later
        self.subscriber = get_subscriber(kwargs['username'])

    def post(self, request, *args, **kwargs):
        # Process view when the form gets POSTed
        pass

    def get_initial(self):
        # Populate ticks in BooleanFields
        initial = {}
        for s in self.subscriber.events.all():
            initial[s.value_id] = True
        return initial

    def get_form(self, form_class):
        # Initialize the form with initial values and the subscriber object
        # to be used in EmailPreferenceForm for populating fields
        return form_class(
            initial=self.get_initial(),
            subscriber=self.subscriber
        )

    def get_success_url(self):
        # Redirect to previous url
        return self.request.META.get('HTTP_REFERER', None)

    def form_valid(self, form):
        messages.info(
            self.request,
            "You have successfully changed your email notifications"
        )
        return super(EmailPreferenceView, self).form_valid(form)

    def form_invalid(self, form):
        messages.info(
            self.request,
            "Your submission has not been saved. Try again."
        )
        return super(EmailPreferenceView, self).form_invalid(form)

email_preferences = EmailPreferenceView.as_view()
```
{% endpullquote %}