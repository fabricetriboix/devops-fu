---
layout: post
title: How to create a minimal PKI with EasyRSA?
date: 2022-09-08
categories: [ssl]
tags: []
---

This short article will demonstrate how to create a root CA, and how
to use that root CA to sign a certificate. We will be using EasyRSA
for that, and we don't want any password anywhere. You can adjust the
number of bits for the key if you want a stronger key. Ditto for the
certificates durations.

{% highlight shell %}
$ curl -sSL https://github.com/OpenVPN/easy-rsa/releases/download/v3.1.0/EasyRSA-3.1.0.tgz | tar xvzf -
$ cd EasyRSA-3.1.0
$ ./easyrsa init-pki
$ ./easyrsa --batch build-ca nopass
$ EASYRSA_CERT_EXPIRE=36500 ./easyrsa build-server-full myservice nopass
{% endhighlight %}

The root CA certificate is available at `pki/ca.crt`. The private key
for `myservice` is available at `pki/private/myservice.key`. The
certificate for `myservice` is available at
`pki/issued/myservice.crt`.
