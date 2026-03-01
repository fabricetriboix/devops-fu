---
layout: post
title: How to create a self-signed SSL certificate?
date: 2018-08-23
categories: [ssl]
tags: [openssl]
---

Do this:

{% highlight shell %}
$ openssl req -nodes -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -subj "/CN=mysite.com"
{% endhighlight %}
