---
layout: post
title: How to base64-encode a file on Windows?
date: 2022-05-16
categories: [shell]
tags: [powershell, windows]
---

On Windows (ewww...), here is how to base-64 encode a file:

{% highlight powershell %}
[convert]::ToBase64String((Get-Content -path "FILE_PATH" -Encoding bytes)) > BASE64_FILE.txt
{% endhighlight %}

UNIX is so much simpler, but sometimes you don't have a choice...
