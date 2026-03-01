---
layout: post
title: A kubectl pattern to upsert an object without a manifest file
date: 2022-05-26
categories: [kubernetes]
tags: []
---

If you have a manifest file, "upserting" an object is easy:

{% highlight shell %}
$ kubectl apply -f MANIFEST.YAML
{% endhighlight %}

But if you want a quick method without a manifest file, just using the
command line, then this (using a secret as an example):

{% highlight shell %}
$ kubectl create secret generic MYSECRET ...
{% endhighlight %}

will work only once, and will fail after that because the object
already exists. The solution is the following:

{% highlight shell %}
$ kubectl create secret generic MYSECRET ... -o yaml --dry-run=client | kubectl apply -f -
{% endhighlight %}

The first `kubectl` command will produce a manifest file to stdout,
and the second `kubectl` command will apply it, instead of just
creating it. This is how you can "upsert" an ojbect without a manifest
file.
