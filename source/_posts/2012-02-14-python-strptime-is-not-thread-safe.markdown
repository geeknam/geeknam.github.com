---
layout: post
title: "Python strptime is not thread-safe"
date: 2012-02-14 15:30
comments: true
categories: [python, fix]
description: "Python strptime is not thread-safe"
keywords: [python, threading, threads, datetime, strptime]
---
When using Python to automate your work, threading is one thing you need to know of. I've got into a problem with threading when parsing time. To be exact: `datetime.datetime.strptime()` function was not thread-safe. Luckily, I found a solution on [StackOverflow](http://stackoverflow.com/questions/4625149/fixed-strptime-exception-with-thread-lock-but-slows-down-the-program). 
<!-- more -->
This is how the problem occurred:

``` python
from datetime import datetime
import threading

def parse_time(value):
    # Parse time with strptime()
    datetime.strptime(value], "%Y-%m-%d %H:%M:%S.%f")

t = threading.Thread(target=parse_time, args=(value))
t.start()
```
Fixing it was pretty straightforward. Just make a dummy call on strptime() before creating a thread:

``` python
datetime.strptime("2011-04-05 18:40:58.525996", "%Y-%m-%d %H:%M:%S.%f")
t = threading.Thread(target=format_time, args=(some_values))
t.start()
```