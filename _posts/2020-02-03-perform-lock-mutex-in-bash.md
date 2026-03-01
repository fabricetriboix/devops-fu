---
layout: post
title: How to perform a lock/mutex in bash
date: 2020-02-03
categories: [shell]
tags: []
---

This can be done using the `ln` command:

{% highlight shell %}
tempfile=$(mktemp ./lock.XXXXXX)
lockfile=./lockfile
trap "rm $lockfile" EXIT
try=1
while ! ln $tempfile $lockfile > /dev/null 2>&1; do
    try=$[ $try + 1 ]
    if [[ $try -gt 10 ]]; then
        echo "ERROR: Timeout trying to lock $cfgfile"
        rm $tempfile
        exit 1
    fi
    sleep 1
done
rm $tempfile
# Protected section
{% endhighlight %}
