---
layout: post
title: How to redirect all stdout/stderr to syslog?
date: 2018-05-24
categories: [sysadmin]
tags: [shell]
---

An easy way to redirect all stdout/stderr of a given process to syslog
is to do the following in a bash script:

{% highlight shell %}
exec 1> >(logger -t MYTAG) 2>&1
{% endhighlight %}

Please note this only works with bash as this is a bash extension to
the POSIX standard.

If you want only stderr:

{% highlight shell %}
exec 2> >(logger -t MYTAG)
{% endhighlight %}

So if you have an application that runs in the foreground (and a
properly written service should not daemonize in my opinion), you can
have the following shell script for a quick solution:

{% highlight shell %}
#!/bin/bash
exec 1> >(logger -t MYTAG) 2>&1
exec /PATH/TO/APP
{% endhighlight %}
