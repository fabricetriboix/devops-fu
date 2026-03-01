---
layout: post
title: "Jinja2: How to iterate over a dictionary?"
date: 2018-05-22
categories: [automation]
tags: [ansible, jinja2]
---

There you go:

{% highlight jinja %}
{% raw %}
{% for key, value in MYDICT.items() %}
{% endfor %}
{% endraw %}
{% endhighlight %}
