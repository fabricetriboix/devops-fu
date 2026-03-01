---
layout: post
title: How to fix 'VirtualBox Client cannot connect to X server' when installing Xubuntu 24 as a guest host?
date: 2024-06-16
categories: [sysadmin]
tags: [ubuntu]
---

There are two bugs that can be seen when installing Xubuntu 24 in a
VirtualBox VM:

 1. The first login always fails after a reboot
 2. After installing the VBox Guest Additions, you will see an error
    message saying "VirtualBox Client cannot connect to X server"

After a lot of research, it seems it's just a bug in Xubuntu. This
[post](https://forum.ubuntu-fr.org/viewtopic.php?id=2084918) (in
French), shows a workaround towards the end:

{% highlight shell %}
$ sudo apt remove libva-wayland2
{% endhighlight %}

This seems very ugly, but at least the two above bugs disappear.
