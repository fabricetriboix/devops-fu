---
layout: post
title: How to install a Debian package non-interactively when it requires user input?
date: 2018-05-21
categories: [automation]
tags: [apt, debian, ubuntu]
---

Install the required package once manually, going through the
configuration options you want. Then, do the following:

{% highlight shell %}
$ sudo apt install debconf-utils
$ sudo debconf-get-selections | grep MYPKG > MYPKG.CONF
{% endhighlight %}

To be able to re-install MYPKG to a new machine without any user
input, copy the MYPKG.CONF file to the new machine and do the
following:

{% highlight shell %}
$ sudo cat MYPKG.CONF | while read line; do echo "$line" | debconf-set-selections; done
$ sudo DEBIAN_FRONTEND=noninteractive dpkg -i MYPKG.deb
{% endhighlight %}

Tada! The package has been installed with the right configuration
options without any user input.
