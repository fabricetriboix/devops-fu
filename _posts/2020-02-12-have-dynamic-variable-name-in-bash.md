---
layout: post
title: How to have a dynamic variable name in bash?
date: 2020-02-12
categories: [shell]
tags: []
---

Code snippet below:

{% highlight shell %}
myvar=something
varname=myvar
echo ${!varname}
{% endhighlight %}
