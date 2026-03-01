---
layout: post
title: How to search for changed content in git history?
date: 2018-06-12
categories: [development]
tags: [git]
---

To search the content of commits, as opposed to the metadata like the
commit message, run this:

{% highlight shell %}
$ git grep REGEX $(git rev-list --all)
{% endhighlight %}

To search the commit messages, run that:

{% highlight shell %}
$ git log --all --grep REGEX
{% endhighlight %}
