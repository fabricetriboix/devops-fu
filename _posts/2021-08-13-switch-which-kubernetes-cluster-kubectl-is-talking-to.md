---
layout: post
title: How to switch which Kubernetes cluster kubectl is talking to?
date: 2021-08-13
categories: [kubernetes]
tags: []
---

Kubectl uses contexts to know which Kubernetes cluster to talk to.

To list all contexts:

{% highlight shell %}
$ kubectl config get-contexts
{% endhighlight %}

To select a given context:

{% highlight shell %}
$ kubectl config use-context CONTEXT_NAME
{% endhighlight %}

To specify the context on the command line every time:

{% highlight shell %}
$ kubectl --context CONTEXT_NAME get nodes
{% endhighlight %}
