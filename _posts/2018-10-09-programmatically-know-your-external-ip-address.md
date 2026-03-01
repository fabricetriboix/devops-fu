---
layout: post
title: How to programmatically know your external IP address?
date: 2018-10-09
categories: [sysadmin]
tags:
---

You are probably used to type "what is my ip" in Google to know this,
but how can you do that in a shell script? Here it is:

{% highlight shell %}
$ curl ifconfig.co
{% endhighlight %}

Simple!
