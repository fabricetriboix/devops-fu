---
layout: post
title: How to inspect a Certificate Signing Request (CSR) using OpenSSL?
date: 2020-06-04
categories: [ssl]
tags: [openssl]
---

Here you go:

{% highlight shell %}
$ openssl req -noout -text -in PATH/TO.CSR
{% endhighlight %}
