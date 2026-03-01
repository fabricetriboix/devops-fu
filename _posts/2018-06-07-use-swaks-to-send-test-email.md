---
layout: post
title: How to use swaks to send a test email?
date: 2018-06-07
categories: [sysadmin]
tags: [smtp]
---

Swaks can be downloaded
[here](http://www.jetmore.org/john/code/swaks/).

Example command line to send a test email:

{% highlight shell %}
./swaks --auth \
        --server SMTP.SERVER.COM \
        --au USERNAME@YOUR_DOMAIN_NAME \
        --ap YOUR_PASSWORD \
        --from USERNAME@YOUR_DOMAIN_NAME \
        --to DEST@EXAMPLE.COM \
        --h-Subject: "This is a test email" \
        --body "Hello, World!"
{% endhighlight %}
