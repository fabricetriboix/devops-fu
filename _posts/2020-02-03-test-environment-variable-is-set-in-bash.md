---
layout: post
title: How to test that an environment variable is set in bash?
date: 2020-02-03
categories: [shell]
tags: []
---

The goal here is to test that a variable both exists and is not empty.
You can use the following to achieve that:

{% highlight shell %}
${THEVAR:+x}
{% endhighlight %}

Which will expand to "x" if `THEVAR` is set and not empty.

Alternatively, this test can be used:

{% highlight shell %}
if [ ! -v THEVAR ]; then
    echo "THEVAR is not set"
fi
{% endhighlight %}

But that test will also pass if `THEVAR` is set but empty.
