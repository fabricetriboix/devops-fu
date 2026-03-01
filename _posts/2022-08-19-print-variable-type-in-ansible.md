---
layout: post
title: How to print a variable type in Ansible?
date: 2018-08-23
categories: [automation]
tags: [ansible, jinja2]
---

This is the way to do it:

{% highlight yaml %}
tasks:
  - debug:
      msg: The type of myvar is {{ myvar | type_debug }}
{% endhighlight %}

That's useful when debugging strange problems in Ansible.
