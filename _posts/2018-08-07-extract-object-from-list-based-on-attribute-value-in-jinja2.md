---
layout: post
title: How to extract an object from a list based on attribute value in Jinja2?
date: 2018-08-07
categories: [automation]
tags: [ansible, jinja2]
---

Use the `selectattr()` filter. For example:

{% highlight jinja %}
{% raw %}
- set_fact:
   myobj: "{{ hostvars[inventory_hostname].ansible_mounts | selectattr('mount', 'equalto', '/') | list }}"
- debug: var=myobj
{% endraw %}
{% endhighlight %}
