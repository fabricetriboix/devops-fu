---
layout: post
title: How to solve "no space left on device" on /boot during an Ubuntu upgrade?
date: 2022-03-29
categories: [sysadmin]
tags: [ubuntu]
---

The reason why `/boot` becomes full is because there are too many Linux
kernels installed. The solution is thus to remove some of them.
Unfortunately, `apt-get dist-upgrade` might not work in this
situation, so we need to use `dpkg` directly.

First, run this command to check what packages you are about to
remove:

{% highlight shell %}
$ sudo dpkg -l 'linux-*' | sed '/^ii/!d;/'"$(uname -r | sed "s/\(.*\)-\([^0-9]\+\)/\1/")"'/d;s/^[^ ]* [^ ]* \([^ ]*\).*/\1/;/[0-9]/!d'
{% endhighlight %}

Make sure you are not about to remove the kernel that you are using
now. You can get the version of the current kernel like so:

{% highlight shell %}
$ uname -r
{% endhighlight %}

When you are ready (meaning you did the necessary backups, etc.),
remove the extra kernels using this command:

{% highlight shell %}
$ sudo dpkg -l 'linux-*' | sed '/^ii/!d;/'"$(uname -r | sed "s/\(.*\)-\([^0-9]\+\)/\1/")"'/d;s/^[^ ]* [^ ]* \([^ ]*\).*/\1/;/[0-9]/!d' | sudo xargs dpkg --remove
{% endhighlight %}
