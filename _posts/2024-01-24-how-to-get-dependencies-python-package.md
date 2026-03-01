---
layout: post
title: How to get the dependencies of a Python package?
date: 2024-01-24
categories: [development]
tags: [python]
---

It is sometimes useful to know what are the dependencies of a Python
package. For example, when building a Docker image, you might want to
know which versions of the dependencies to install.

Here you go:

{% highlight shell %}
$ curl https://pypi.org/pypi/${PACKAGE_NAME}/${PACKAGE_VERSION}/json | jq
{% endhighlight %}
