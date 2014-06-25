---
layout: post
title: "Python class factory"
date: 2012-02-16 12:05
comments: true
categories: [python]
description: "More on Python factory design pattern"
keywords: "python, class, factory, design, pattern, design pattern, inheritance, dynamic"
---
Today's post will be on factory design pattern in Python. You can read more about it in [Wiki](http://en.wikipedia.org/wiki/Factory_method_pattern).
This design pattern allows you to create objects without specifying the exact class of object that will be created. Let's say, you want to create an object based on a provided string.
 
Here are some classes. Both of them are pets. We want to dynamically create a pet from one of those classes based on a string.
<!-- more -->
``` python
class Pony(object):
    
    def __init__(self, name):
        self.name = name
        self.hp = 100
    
    def magic_power(self):
        # Attack opponent with super powers
        pass
    
    def heal(self):
        # Increase hp (health point)
        pass

class Snake(object):
    # Blah blah same as Pony
    pass
```

Let's write a factory method to make this happen:

``` python
def create_class(pet_class_name):
    # pet_class_name decides what class will be returned
    if pet_class_name is 'pony':
        class InheritedClass(Pony):
            # You can add additional methods or attributes if needed
            pass
        return InheritedClass
    elif pet_class_name is 'snake':
        # Or you can return the exact class
        return Snake

# Create MyPet from a string
MyPet = create_class('pony')

# MyPet is now a subclass of Pony
# It inherits everything from Pony
django = MyPet('django')

# Kaboom !!!
django.magic_power()
``` 