---
layout: post
title: How to list all Android packages?
date: 2025-01-05
categories: [android]
tags: []
---

Connect your Android devices to your PC using `adb`, then run:

{% highlight shell %}
$ adb shell pm list packages -f
{% endhighlight %}
