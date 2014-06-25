---
layout: post
title: "TonidoPlug"
date: 2010-11-28 15:19
comments: true
categories: [linux]
description: "My new TonidoPlug"
keywords: "linux, plug computers"
---
Yes, [TonidoPlug](http://tonidoplug.com "TonidoPlug") is a new toy that I've gotten last week. It is a basically a plug computer that can act likea home server. The hardware specs are the same as SheevaPlug with 1.2GHz ARM and 512MB RAM and 512MB Flash. The best thing
about it is the energy consumption ranging from 5-10 Watts.
<!-- more -->
So what's the difference between TonidoPlug and SheevaPlug? The main and the most noticeable difference is the built-in
software that TonidoPlug provides which makes it really easy to get started with. It's seriously just plug-and-play and
you will get all kinds of stuffs you need: torrent (yes torrenting 24/7 is leet), music player (that allows streaming),
sharing files and photos, mounting drives.

But the problem with this is the limitation of applications you can install. What if you want to run an rsync server or
install a LAMP stack? Luckily there is an open source project called PlugApps that allows users to have more
flexibility. Some kind souls have compiled Arch Linux and port it to work on a SheevaPlug hardware. They named it
Plugbox. SheevaPlug naturally runs on Ubuntu 9.04 but apparently Ubuntu has dropped support for ARM that's why Arch
Linux would be a perfect suit.

I'm currently trying to run Plugbox from my external hard drive that has 2 partitions but TonidoPlug doesn't seem to
boot from it. I'm gonna dive deeper into this. Will update the blog once I get it to work.

__Edited__  
Ok, like finally I got TonidoPlug booting from external hard drive but it still runs on Tonido (Ubuntu 9.04). There's
no way I can make it boot from  external hard drive with PlugBox. The problem was with reboot command, I had to make a
cold boot (plug and unplug the power source) in order for the plug to boot into my Samsung G3. I've recently installed awesome
stuff like:

- webmin (for sysadmin)
- gitosis (for git repos)
- mt-daapd (iTunes Server)
- ettercap (to monitor my home network)
- lsyncd (to synchronize files)
- nginx (for reverse proxy and web server)
- django (web apps running with gunicorn and postgres)
- transmission (torrent server)
- vncserver
