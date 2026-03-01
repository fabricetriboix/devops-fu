---
layout: post
title: In a Python script, how to change to the directory containing that script?
date: 2020-03-15
categories: [development]
tags: [python]
---

Code snippet below:

{% highlight python %}
import os
tmp = os.path.abspath(__file__)
tmp = os.path.dirname(tmp)
os.chdir(tmp)
{% endhighlight %}
