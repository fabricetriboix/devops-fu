---
layout: post
title: A useful ps command
date: 2019-05-10
categories: [sysadmin]
tags:
---

You might want to make an alias of it:

{% highlight shell %}
$ ps -eH -o user,pid,ppid,state,pcpu,pmem,vsize,start_time,time,nlwp,cmd
{% endhighlight %}
