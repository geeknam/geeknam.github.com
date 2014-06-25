---
layout: post
title: "Fabric deployment"
date: 2011-11-12 13:28
comments: true
categories: [python, fabric, django]
description: "Fabric deployment"
keywords: "python, fabric, deployment, django"
---
Having played with Capistrano before, I suddenly had an urge to find its Pythonic alternative. After few google searches, I stumbled upon Fabric. It's basically a "python library and command-line tool for streamlining the use of SSH for application deployment or systems administration tasks". Spent few minutes trying to write functions to deploy Django applications to my Linux server and I have to say, Fabric's API is really easy to use, very customizable (it's as easy as wrapping up your shell commands in a python function) and more importantly it has Django integration which I'm gonna need the most. Having said all these, here's the video demo I recorded while messing around with Fabric, enjoy:

P/S: django-nose, nosegrowl2 and tdaemon were used in order to show notification of testing results (just FYI) 

<object class="BLOGGER-youtube-video" classid="clsid:D27CDB6E-AE6D-11cf-96B8-444553540000" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0" data-thumbnail-src="http://i.ytimg.com/vi/xtmb-5WPWQs/0.jpg" height="266" width="320"><param name="movie" value="http://www.youtube.com/v/xtmb-5WPWQs?version=3&f=user_uploads&c=google-webdrive-0&app=youtube_gdata" />
<param name="bgcolor" value="#FFFFFF" />
<embed width="620" height="420"  src="http://www.youtube.com/v/xtmb-5WPWQs?version=3&f=user_uploads&c=google-webdrive-0&app=youtube_gdata" type="application/x-shockwave-flash"></embed></object>
