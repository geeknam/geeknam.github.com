---
layout: post
title: "Regenerating Google's ClientLogin AuthToken in Django"
date: 2012-02-14 12:47
comments: true
categories: [google, django, python, api]
description: "Regenerating Google's ClientLogin AuthToken in Django"
keywords: "google, api, clientlogin, auth, token, python, django"
---
If your application makes use of Google API, you will most likely have to deal with AuthToken expiry (unless you use oAuth). As far as I know, AuthToken generated with ClientLogin authorization expires after 2 weeks. Therefore, it's good to have a solution to auto regenerate it. First of all, create a model that stores the token and the last updated day.
<!-- more -->

``` python
from django.db import models
from datetime import datetime, timedelta

class AuthToken(models.Model):
    token = models.TextField()
    last_updated = models.DateTimeField(auto_now_add=True)

    def token_update(self):
        # Check if the token's lifespan is more than n days (n=1 in this case)
        # You can adjust n value to whatever you want

        if datetime.now() - self.last_updated > timedelta(days=1):
            return True
        return False
```

Now let's write a function that regenerates AuthToken and saves it back to database.

```python
from models import AuthToken

CLIENT_LOGIN_URL = 'https://www.google.com/accounts/ClientLogin'

def regenerate_authtoken():
    auth_token = AuthToken.objects.all()[0]
    if auth_token.token_update():
        data = urllib.urlencode(
            {
                'Email': 'youremail@gmail.com',
                'Passwd': 'yourpassword',
                'service': 'service_name',
                'accountType': 'GOOGLE'
            }
        )
        req = urllib2.Request(CLIENT_LOGIN_URL, data=data)
        resp = urllib2.urlopen(req)
        resp_body = resp.read()

        resp_dict = dict(
            x.split('=') for x in resp_body.split('\n') if x
        )
        auth_token.token = resp_dict['Auth']
        auth_token.save()
```

With this approach you would have to call `regenerate_authtoken()` every time you make API requests. Let me know if you have a better solution.