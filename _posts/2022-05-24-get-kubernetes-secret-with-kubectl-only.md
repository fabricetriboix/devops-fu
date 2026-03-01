---
layout: post
title: How to get a Kubernetes secret with kubectl only?
date: 2022-05-24
categories: [kubernetes]
tags: []
---

Kubectl allows you to use go-templates, so you can do this:

{% highlight shell %}
{% raw %}
$ kubectl get secret/MYSECRET -o 'go-template={{ .data.NAME | base64decode }}'
{% endraw %}
{% endhighlight %}
