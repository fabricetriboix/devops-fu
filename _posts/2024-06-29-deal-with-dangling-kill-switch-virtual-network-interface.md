---
layout: post
title: How to deal with a dangling kill switch virtual network interface?
date: 2024-06-29
categories: [sysadmin]
tags: []
---

I tried to uninstall ProtonVPN but unfortunately it didn't uninstall
properly and left a dangling `pvpn*` network interface that blackholes
your entire traffic, which is a leftover from the kill switch feature.

It's likely that ProtonVPN left a connection configuration with the
NetworkManager for the kill switch feature. You can check it like so:

{% highlight shell %}
$ sudo nmcli con show
{% endhighlight %}

If one of the connection looks like `pvpn-killswitch`, that's the
culprit. To delete it, run:

{% highlight shell %}
$ sudo nmcli con del pvpn-killswitch
{% endhighlight %}

And your problem should hopefully be fixed.
