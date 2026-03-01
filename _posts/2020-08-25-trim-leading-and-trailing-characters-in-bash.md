---
layout: post
title: How to trim leading and trailing characters in Bash?
date: 2020-08-25
categories: [shell]
tags: [bash]
---

Bash has built-in facilities to trim characters at the beginning and
end of the value of a variable:

{% highlight shell %}
{% raw %}
$ myvar="AAAthis is a testAAA"
$ echo ${myvar##*(A)}
this is a testAAA
$ echo ${myvar%%*(A)}
AAAthis is a test
{% endraw %}
{% endhighlight %}
