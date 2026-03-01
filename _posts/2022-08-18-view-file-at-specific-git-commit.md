---
layout: post
title: How to view a file at a specific git commit?
date: 2022-08-18
categories: [development]
tags: [git]
---

This is the way:

{% highlight shell %}
$ git show REV:/path/to/file
{% endhighlight %}

For example, if the git commit starts with "ab12" and the file is
bob.py in the current directory, type this:

{% highlight shell %}
$ git show ab12:./bob.py
{% endhighlight %}
