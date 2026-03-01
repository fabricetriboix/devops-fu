---
layout: post
title: How to iterate an array in Bash?
date: 2020-10-10
categories: [shell]
tags: [bash]
---

Here you go:

{% highlight shell %}
myarray[0]="this is a test"
myarray[1]="this is another test"
for i in "${myarray[@]}"; do
    echo "Value: $i"
done
{% endhighlight %}

Don't forget the quotes around the `${myarray[@]}`, or the spaces will
separate each word on its own item.
