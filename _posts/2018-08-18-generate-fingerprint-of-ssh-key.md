---
layout: post
title: How to generate a fingerprint of an SSH key?
date: 2018-08-18
categories: [sysadmin]
tags: [ssh]
---

Run the following:

{% highlight shell %}
$ ssh-keygen -l -f /PATH/TO/PUBLIC/KEY
{% endhighlight %}

Or for the old MD5 fingerprint:

{% highlight shell %}
$ ssh-keygen -l -E md5 -f /PATH/TO/PUBLIC/KEY
{% endhighlight %}
