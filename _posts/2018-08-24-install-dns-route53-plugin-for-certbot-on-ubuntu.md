---
layout: post
title: How to install the dns-route53 plugin for certbot on Ubuntu?
date: 2018-08-24
categories: [ssl]
tags: [certbot, letsencrypt]
---

There are no instruction on how to install the [dns route53
plugin](https://certbot-dns-route53.readthedocs.io/en/latest/) for
certbot. Here is how to do it for Ubuntu. To install certbot:

{% highlight shell %}
$ sudo apt update
$ sudo apt install software-properties-common
$ sudo apt-add-repository ppa:certbot/certbot
$ sudo apt update
$ sudo apt install certbot
{% endhighlight %}

To install the dns-route53 plugin:

{% highlight shell %}
$ sudo apt install python3-pip
$ sudo pip3 install certbot-dns-route53
{% endhighlight %}

You can then create a new certificate with something like that:

{% highlight shell %}
$ sudo certbot certonly --dns-route53 -d YOUR-DOMAIN.com --deploy-hook 'systemctl reload apache2'
{% endhighlight %}
