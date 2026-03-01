---
layout: post
title: How to persist variables in a loop from a find command?
date: 2020-12-21
categories: [shell]
tags: [bash]
---

The usual way is to use a pipe:

{% highlight shell %}
find . -name '*.jpg' | while read f do;
    some_command "$f"
done
{% endhighlight %}

There are two problems here. First, this won't work if paths have
spaces or other blank characters in them. Second, bash actually forks
a sub-process when using the pipe, so everything that is done within
the "while" loop is executed in a sub-process. If variables are set in
this loop, they won't be available outside the loop.

Here is a better way:

{% highlight shell %}
a=
i=0
while IFS= read -r -d $'\0' f; do
    a[i++]="$f"
done < <(find . -name '*.jpg' -print0)
{% endhighlight %}

The `-print0` option for `find` will separate entries using the
null character instead of the newline character, thus allowing spaces
and other blanks in path names. Using redirection in the example above
runs `find` in a subshell, rather than the loop itself, thus
preserving variables after the loop.
