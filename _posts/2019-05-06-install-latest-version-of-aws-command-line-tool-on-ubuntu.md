---
layout: post
title: How to install the latest version of AWS command line tool on Ubuntu?
date: 2019-05-06
categories: [aws]
tags: [ubuntu]
---

Follow the steps:

{% highlight shell %}
$ sudo apt update
$ sudo apt install python python-pip
$ sudo pip install awscli
{% endhighlight %}

This will install a reasonably recent version of awscli, at least more
recent than installing directly with `$ sudo apt install awscli`.
