---
layout: post
title: How to set the executable bit on directories but not files?
date: 2022-05-24
categories: [shell]
tags: []
---

For a long time, I was using contrieved methods to recursively change
permissions. If you add the `x` bit, directories will be searchable,
but all files will be marked as executables, which is probably not
what you want. The magic is to use an uppercase `X` like so:

{% highlight shell %}
$ chmod -R u=rwX,g=rX,o=rX MYDIR
{% endhighlight %}

The `X` will instruct `chmod` to set the executable bit on directories
and files that already have that bit set, but it will not set it on
files that don't have that bit set.
