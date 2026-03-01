---
layout: post
title: How to loop variables with spaces in bash?
date: 2022-06-16
categories: [shell]
tags: [bash]
---

I had this problem when I wanted to loop the arguments of a script,
and those arguments could have spaces in them. The solution is to
first convert the arguments into an array, and then loop the array by
using the array notation:

{% highlight shell %}
args=( "$@" )
for arg in "${args[@]}; do
    echo "$arg"
done
{% endhighlight %}

