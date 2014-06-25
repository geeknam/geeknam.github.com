---
layout: post
title: "Fibonacci with caching"
date: 2012-05-29 19:21
comments: true
categories: [python]
description: "Writing a faster fibonacci"
keywords: "python, memoize, caching, cache, defaultdict, decorator, performance, optimize, faster, fibonacci, fibo"
---

Here is my attempt to compare the performance of `fibonacci` with different ways of caching the results. 
First implementation uses memoization decorator whereas the second one makes use of `defaultdict` for caching. You can read my previous blog [post](/2012/02/15/python-caching-using-defaultdict/) on caching with `defaultdict`.


``` python Fibonacci 1
import timeit

def memoize(function):
    memo = {}

    def wrapper(*args):
        if args in memo:
            return memo[args]
        else:
            rv = function(*args)
            memo[args] = rv
            return rv

    return wrapper


@memoize
def fib(num):
    if num == 0:
        return 0
    elif num == 1:
        return 1
    else:
        return fib(num - 1) + fib(num - 2)


t = timeit.Timer(stmt="fib(150)", setup="from __main__ import fib")
print t.timeit()

# Approximate Result: 0.482068061829 seconds
```
<!-- more -->

``` python Fibonacci 2
from collections import defaultdict
import timeit

class FibCache(defaultdict):

    def __init__(self, fn):
        self.fn = fn

    def __missing__(self, num):
        self[num] = self.fn(num, self)
        return self[num]


def fib(num, cache):
    if num == 0:
        return 0
    elif num == 1:
        return 1
    else:
        return cache[num - 1] + cache[num - 2]


fib_cache = FibCache(fib)

t = timeit.Timer(stmt="fib_cache[150]", setup="from __main__ import fib_cache")
print t.timeit()

# Approximate Result: 0.268569946289 seconds
```
__Note__: Default number of loops for timeit is 1000000

If you're looking to hire a Python programmer, this would be a great programming question. Challenge all the candidates to write the fastest fibonacci function. Hire the one whose code is the fastest.

Feel free to suggest better/faster implementations if you know any, I'd be glad to add more to the collection.