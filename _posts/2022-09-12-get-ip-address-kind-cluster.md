---
layout: post
title: How to get the IP address of a kind cluster?
date: 2022-09-12
categories: [kubernetes]
tags: [kind]
---

Once you start a kind cluster, you probably want to know how you can
access services that are accessible from outside, such as ingresses.
Here is the way to get its local IP address:

{% highlight shell %}
{% raw %}
$ docker container inspect kind-control-plane --format '{{ .NetworkSettings.Networks.kind.IPAddress }}'
{% endraw %}
{% endhighlight %}
