---
layout: post
title: How to create a Let’s Encrypt SSL certificate using DNS challenge?
date: 2019-04-23
categories: [ssl]
tags: [certbot, letsencrypt]
---

First of all, register the domain name if not done already. Then run
this:

{% highlight shell %}
$ sudo certbot -d YOUR.DOMAIN.COM --manual --preferred-challenges dns certonly
{% endhighlight %}

When prompted, create the required TXT record with your DNS provider
and press enter in certbot.
