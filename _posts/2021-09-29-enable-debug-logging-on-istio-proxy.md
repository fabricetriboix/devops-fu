---
layout: post
title: How to enable debug logging on an Istio proxy?
date: 2021-09-29
categories: [kubernetes]
tags: [istio]
---

To turn on debug logging for a given pod, and assuming you use the
default Istio configuration regarding injecting side-car proxies:

{% highlight shell %}
$ kubectl exec -it PODNAME -c istio-proxy -- curl -X POST 'http://localhost:15000/logging?level=debug'
{% endhighlight %}
