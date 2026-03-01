---
layout: post
title: How to generate an MD5 checksum for a file on Windows?
date: 2022-08-09
categories: [shell]
tags: [powershell, windows]
---

On Windows (ewww...), here is how to generate an MD5 checksum for a
file:

{% highlight powershell %}
Get-FileHash "FILE_PATH" -Algorithm MD5
{% endhighlight %}
