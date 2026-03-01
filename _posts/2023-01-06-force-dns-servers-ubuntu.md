---
layout: post
title: How to force DNS servers in Ubuntu?
date: 2023-01-06
categories: [sysadmin]
tags: [ubuntu]
---

I periodically have issues with the DNS setup on my Ubuntu desktop. I
finally decided to address this issue by forcing the DNS resolution to
go through DNS servers I specify, rather than using the default of
`systemd-resolved`. To do this, here are the steps:

{% highlight shell %}
$ sudo systemctl stop systemd-resolved.service
$ sudo systemctl disable systemd-resolved.service
$ sudo rm /etc/resolv.conf  # remove symlink
$ sudo -e /etc/resolv.conf  # see (1)
$ sudo -e /etc/NetworkManager/NetworkManager.conf  # see (2)
{% endhighlight %}

About (1), edit your resolv.conf file and set the DNS servers you
want, for example:

{% highlight conf %}
# Control D
nameserver 76.76.2.0
nameserver 76.76.10.0
{% endhighlight %}

The final step (2) is to prevent NetworkManager from overwriting your
resolv.conf file. In order to do that, add the following under the
`[main]` section:

{% highlight conf %}
dns=none
rc-manager=unmanaged
{% endhighlight %}

Reboot the computer and that should be it (and permanent).
