---
layout: post
title: How to display the content of an SSL certificate?
date: 2018-06-19
categories: [ssl]
tags: [openssl]
---

Run this:

{% highlight shell %}
$ openssl x509 -in SSL-CERTIFICATE.PEM -text
{% endhighlight %}
