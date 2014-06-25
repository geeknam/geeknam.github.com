---
layout: post
title: "Burn your fixtures, use model factories"
date: 2012-04-21 16:46
comments: true
categories: [python, django]
description: "Burn your fixtures, use model factories"
keywords: "python, django, fixtures, testing, unittest, model, factory, best practises"
---
This blog post is inspired by one of the talks in PyCon US 2012 by [Carl Meyer](http://twitter.com/carljm) named [Testing and Django](http://pyvideo.org/video/699/testing-and-django) in which he discusssed the guidelines for writing good tests.

After watching the whole talk, I realised what I've been doing is all wrong. The fact that tests I write run within minutes shows how terrible my unit-test-fu is. Using fixtures was a really bad idea. Each test case I wrote would load some fixtures to setup necessary dependencies. Sometimes, this creates a problem when the model has been redesigned. The fixtures would have to be edited due to the change of the model otherwise they won't be installed properly.

Instead of using fixtures, Carl introduced `model factories` which would create a model instance with default values. Of course, you will have a flexibility to change those default values to suit each of your test because naturally, when you test a behaviour of a class, you definitely need it to be in different conditions.
<!-- more -->
Here is an example of a model factory that was used in the talk:

``` python
def create_profile(**kwargs):
    defaults = {
        "likes_cheese": True,
        "age": 32,
        "address": "3815 Brookside Dr",
    }
    defaults.update(kwargs)
    if "user" not in defaults:
        defaults["user"] = create_user()
    return Profile.objects.create(**defaults)
```

And this is how you could use it in the test:

``` python
def test_can_vote(self):
    """A user age 18+ can vote in the US."""
    profile = create_profile(age=18)
    self.assertTrue(profile.can_vote)
```

This approach solves the problem with maintaining the fixtures to suit your model design. If your model changes, the only thing you have to edit is the default values of the factory. Therefore `model factories` are way easier to maintain. If you need a large data set, you could easily write a for loop that wraps the model factory. So burn all your fixture right now !!!