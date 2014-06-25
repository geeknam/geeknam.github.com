---
layout: post
title: "Fix screwed up keyboard layout on Ubuntu 9.04"
date: 2010-12-05 13:34
comments: true
categories: [linux, fix]
description: "How to fix keyboard layout mapping for Ubuntu 9.04"
keywords: "linux, ubuntu, vnc, keyboard, fix"
---
Got Gnome desktop installed on TonidoPlug but when trying to vnc to it I realised the keyboard layout is messed up. This is how you fix it:

1. Kill existing vnc session: `vncserver -kill :1` (1 here is the pid)
2. Edit with: `nano /root/.vnc/xstartup`
3. Add this line: `export XKL_XMODMAP_DISABLE=1`
4. Restart vncserver