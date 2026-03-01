---
layout: post
title: How to increase the verbosity of a Istio Envoy sidecar proxy?
date: 2025-07-25
categories: []
tags: []
---

You will need `istioctl`:

{% highlight shell %}
$ istioctl proxy-config log $POD.$NAMESPACE --level debug
{% endhighlight %}
