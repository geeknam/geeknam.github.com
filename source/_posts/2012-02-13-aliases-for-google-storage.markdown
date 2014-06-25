---
layout: post
title: "Aliases for Google Storage"
date: 2011-06-11 13:13
comments: true
categories: [google, linux] 
description: "Useful bash alias for Google Storage"
keywords: "google, bash, alias, linux"
---
I've just registered an account on Google Storage last month and I totally love it as. I particularly like gsutil since I'm most of my time on Terminal. Here are two aliases I use with gsutil in my `.bash_profile`. Of course, remember to replace your bucket name

``` bash
function gsup(){
    gsutil cp "$@" gs://bucketname.com
}

function gsdown(){
    gsutil cp gs://bucketname.com/"$@" ~/Desktop
}
```