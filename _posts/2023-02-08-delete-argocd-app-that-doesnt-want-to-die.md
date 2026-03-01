---
layout: post
title: How to delete an ArgoCD app that doesn't want to die?
date: 2023-02-08
categories: [kubernetes]
tags: [argocd]
---

Try this:

{% highlight shell %}
$ argocd app set $APPNAME --allow-empty
{% endhighlight %}
