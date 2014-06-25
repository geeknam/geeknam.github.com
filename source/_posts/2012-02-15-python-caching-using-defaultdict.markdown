---
layout: post
title: "Python caching using defaultdict"
date: 2012-02-15 10:46
comments: true
categories: [python, django]
description: "Python caching using defaultdict"
keywords: "django, python, cache, caching, defaultdict, collections, optimization"
---
Today, I've learnt about `defaultdict` container from my colleague, who uses it for caching. `defaultdict` container is part of `collections` module which provides high-performance container datatypes.
You can find more about it [here](http://docs.python.org/library/collections.html#collections.defaultdict). __Note__: New in version 2.5.
<!-- more -->
Let's see the code without caching mechanism:

``` python
import csv
from models import Pony, PonySwag

# Getting some data from csv file
swags = csv.DictReader('pony_swag.csv', 'rU'), dialect='excel')

for swag in swags:
    # Get or create a pony
    pony = Pony.objects.get_or_create(
        name=swag['pony_name']
    )[0]

    # Create pony swag which belongs to a pony
    PonySwag.objects.get_or_create(
        name=swag['swag_name'],
        power=swag['swag_power'],
        pony=pony 
    )[0]
``` 

In this scenario, for every entry in csv, the code hits the database to get a pony (based on the name) to assign it to a corresponding swag. Now if we cache the created ponies, we would not have
to query the database anymore.

``` python
import csv
from collections import defaultdict
from models import Pony, PonySwag

class PoniesCache(defaultdict):
    # If dict lookup fails this will be called
    def __missing__(self, key):
        # Insert a pony in cache dict and return it
        pony = Pony.objects.get_or_create(name=key)[0]
        self[key] = pony
        return pony

ponies_cache = PoniesCache()
# Getting some data from csv file
swags = csv.DictReader('pony_swag.csv', 'rU'), dialect='excel')

for swag in swags:
    pony = ponies_cache[swag['pony_name']]

    # Create pony swag which belongs to a pony
    PonySwag.objects.get_or_create(
        name=swag['swag_name'],
        power=swag['swag_power'],
        pony=pony 
    )[0]
```

As you might have read, `defaultdict` is a subclass of `dict` and it overrides a method called `__missing__`.  
`__missing__` is called by the `__getitem__()` method of the dict class when the requested key is not found (when `swag['pony_name']` value is not found in ponies_cache).
In our case, we override `__missing__` to create a pony and store it in cache (if it doesn't exist yet) and return it. This results in a pony being inserted in the dictionary with a key `swag['pony_name']`.
