---
layout: post
title: How to test a service account permissions in Kubernetes?
date: 2021-03-17
categories: [kubernetes]
tags: []
---

To test access to a certain configmap, for example, run something like
this:

{% highlight shell %}
$ kubectl --as=system:serviceaccount:MY_NAMESPACE:MY_SERVICE_ACCOUNT auth can-i get configmap/MY_CONFIGMAP
{% endhighlight %}

Replace `get` with other verbs as necessary.
