---
layout: post
title: How to create a virtual environment in Python3?
date: 2019-03-29
categories: [development]
tags: [python]
---

Install whatever Python3 package you want for your OS, along with pip.
For example for Ubuntu:

{% highlight shell %}
$ sudo apt update
$ sudo apt install python3 python3-pip
{% endhighlight %}

Go into a directory you want to use as a base and run the following:

{% highlight shell %}
$ python3 -m venv venv
$ source ./venv/bin/activate
$ pip install -U pip setuptools wheel
{% endhighlight %}

The last line is important to avoid compatibility problems when
installing new packages with pip.
