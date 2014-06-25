---
layout: post
title: "Writing unit tests for Django middleware"
date: 2012-05-13 02:21
comments: true
categories: [python, django]
description: "Writing unit tests for Django middleware"
keywords: "python, django, testing, unittest, mock, mocking, test driven development, middleware"
---
Sometimes, it is a pain in the ass to write tests for Django apps (even though Django provides some pretty awesome testing tools out of box). One of the scenarios is testing middleware. Let's take a look at this middleware.

``` python
from .cart import Cart

class CartMiddleware:
    def process_request(self, request):
        '''Load the current cart from the session, or create a new one'''
        cart = request.session.get('cart', None)

        if cart is None:
            cart = Cart()
        else:
            cart = Cart(**cart)

        setattr(request, 'cart', cart)
        return None

```
<!-- more -->
__What the middleware does:__  
- It gets attributes/values of the cart from the session  
- It creates a cart with those unpacked arguments or creates a fresh cart and attaches this cart to the request object.

__Now, let's analyze what needs to be tested:__  
1. Assert that if the cart exists in the session, it should be set as an attribute of the request  
2. Assert that if the cart doesn't exist in the session, a new cart is created and attached to the request object  
3. Assert that all attributes of the cart can be accessed after the cart is attached to the request object

__How about dependencies? What do we need in order to write this test?__  
- CartMiddleware class (this can be easily imported)  
- `request` object as an argument in process_request(). This one is hard to obtain, and since we are writing a unit test, let's just mock it.

We are now ready to write the test

``` python
from django.test import TestCase
from mock import Mock
from .cart import CartMiddleware, Cart

class CartMiddlewareTest(TestCase):

    def setUp(self):
        self.cm = CartMiddleware()
        self.request = Mock()
        self.request.session = {}

```

This sets up an instance of CartMiddleware and mocks a request. I'm using Michael Foord's `mock` library to assist me with this. Since we know `session` is a dictionary like object, we can mock it with an empty dictionary for now.

``` python 
    def test_process_request_without_cart(self):
        self.assertEqual(self.cm.process_request(self.request), None)
        self.assertIsInstance(self.request.cart, Cart)

    def test_process_request_with_cart(self):
        data = {'postcode': '3050'}
        self.request.session = {'cart': data}
        self.assertEqual(self.cm.process_request(self.request), None)
        self.assertIsInstance(self.request.cart, Cart)
        self.assertEqual(self.request.cart.postcode, data['postcode'])
```

__The first test asserts that (without a cart in the session):__  
- process_request returns None  
- Cart object has been attached to request as an attribute

__The second test asserts that (with a cart in the session):__  
- process_request returns None  
- Dictionary containing cart attributes in `session` is unpacked and used to create a Cart object.  
- The cart attribute (postcode in this case) can be accessed


__Conclusion__  
As you could see, I didn't have to use `django.test.client.Client` to create requests, responses or sessions. When writing unit tests, one shouldn't care much about creating a precise dependency because, eventually what needs to be tested is the behaviour of the code and not the dependency itself. Therefore, it is more advisable to mock those dependencies in order to satisfy the conditions of the unit of code we test.