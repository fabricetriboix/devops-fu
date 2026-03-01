---
layout: post
title: How to check that a certificate and a key match?
date: 2022-08-30
categories: [ssl]
tags: [openssl]
---

In TLS/SSL, things can't work if the certificate doesn't match the
private key. It's rare to end up in such a situation, but it's still
good to know how to check that the certificate has been issued for the
correct private key. Otherwise, you will end up with strange,
senseless errors. The method below works only for RSA keys, though.
Here you go:

{% highlight shell %}
$ openssl x509 -noout -modulus -in /PATH/TO/CERTIFICATE
$ openssl rsa -noout -modulus -in /PATH/TO/PRIVATE/KEY
{% endhighlight %}

Both values should match. If they don't, that means that the
certificate has been generated for another private key and SSL can't
work.
