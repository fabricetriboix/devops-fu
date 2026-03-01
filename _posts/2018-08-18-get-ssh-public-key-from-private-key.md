---
layout: post
title: How to get the SSH public key from the private key?
date: 2018-08-18
categories: [sysadmin]
tags: [ssh]
---

Run the following to get the SSH public key from the private key:

{% highlight shell %}
$ ssh-keygen -y -f /PATH/TO/PRIVATE/KEY
{% endhighlight %}
