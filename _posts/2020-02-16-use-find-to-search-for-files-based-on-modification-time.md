---
layout: post
title: How to use `find` to search for files based on modification time?
date: 2020-02-16
categories: [shell]
tags: []
---

Example to find the files older than 17 days

{% highlight shell %}
$ find . -daystart -mtime +17 -print
{% endhighlight %}

The reason to use `-daystart` is to make things more natural for human
beings, because `find` performs some rounding. From the man page:

> When find figures out how many 24-hour periods ago the file was last
> accessed, any fractional part is ignored, so to match -atime +1, a
> file has to have been accessed at least two days ago.

To find files that were accessed exactly 17 days ago (that is: that
have been accessed between 00:00 and 23:59:59 on the day that was 17
days ago):

{% highlight shell %}
$ find . -daystart -mtime 17 -print
{% endhighlight %}
