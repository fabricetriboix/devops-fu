---
layout: post
title: How to generate a comma-separated list in Jinja2?
date: 2018-05-20
categories: [automation]
tags: [ansible, jinja2]
---

From a list directly:

{% highlight jinja %}
{% raw %}
{{ somelist|join(",") }}
{% endraw %}
{% endhighlight %}

From a list of objects:

{% highlight jinja %}
{% raw %}
{% for x in somelist %}{{ x.xyz }}{% if not loop.last %},{% endif %}{% endfor %}
{% endraw %}
{% endhighlight %}
