---
layout: post
title: How to list the system certificates on Linux?
date: 2024-04-09
categories: [ssl]
tags: []
---

Here is a way to list a summary of the certificates present in the
`/etc/ssl/certs/ca-certificates.crt` file:

{% highlight shell %}
$ awk -v decoder='openssl x509 -noout -subject -enddate 2>/dev/null' '
  /BEGIN/{close(decoder)};{print | decoder}
' < /etc/ssl/certs/ca-certificates.crt
{% endhighlight %}
