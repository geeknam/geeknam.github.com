---
layout: post
title: "GAE XMPPHandler"
date: 2010-12-25 15:57
comments: true
categories: [google, appengine, python]
description: "Get email from IM formatted string in Google App Engine XMPPHandler"
keywords: "google, appengine, xmpp, python"
---
Finally got time to continue developing Push Contacts. I've gotten a nerve-wrecking problem while developing with C2DM
applications. When the app supposed to get the push message, it didn't respond to it. Looking at the logs I encountered
an error stack related to GTalk (with TalkProvider tag). Decided to google the problem and the solution was to sign out
of GTalk on the phone.
<!-- more -->
Ok now a tip related to Google App Engine development. I just added a new feature to Push Contacts which is allowing
users to reply to the latest SMS received. I had to retrieve the email of the user the bot is chatting with to find
which phone number to send the SMS to. Took a look at guru.appspot.com source code and decided to use
`xmpp_handlers.CommandHandler`. Used this method `text_message(self, message)` and message.sender supposed to return the
sender of a message but instead it returned an IM formatted string. Here is how you can retrieve the email.

``` python
class XMPPHandler(xmpp_handlers.CommandHandler):
    def text_message(self, message):
        idx  = message.sender.index('/')
        user = message.sender[0:idx]
        logging.debug(user)
```
