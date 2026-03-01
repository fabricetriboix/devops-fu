---
layout: post
title: How to convert Maildir to mbox?
date: 2019-02-26
categories: [sysadmin]
tags:
---

Make sure you have the `procmail` package installed, and then run the
following:

{% highlight shell %}
$ for i in MAILDIR/cur/*; do formail -I "Status: RO" < "$i" >> MBOX; done
{% endhighlight %}
