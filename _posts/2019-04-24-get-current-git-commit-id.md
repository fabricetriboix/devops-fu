---
layout: post
title: How to get the current git commit id?
date: 2019-04-24
categories: [development]
tags: [git]
---

Do this:

{% highlight shell %}
$ git rev-parse HEAD
{% endhighlight %}

This gives you the full commit id, for the short id, run this:

{% highlight shell %}
$ git rev-parse --short HEAD
{% endhighlight %}
