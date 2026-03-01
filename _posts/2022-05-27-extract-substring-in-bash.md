---
layout: post
title: How to extract a substring in Bash?
date: 2022-05-27
categories: [shell]
tags: [bash]
---

Bash can extract a substring using the syntax `${VAR:START:LENGTH}`.
The `START` is the index of the first character, starting at 0. For
example:

{% highlight shell %}
$ string=abcd1234
$ echo "${string:4:3}
123
{% endhighlight %}
