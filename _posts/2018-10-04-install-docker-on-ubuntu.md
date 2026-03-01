---
layout: post
title: How to install Docker on Ubuntu?
date: 2018-10-04
categories: [containers]
tags: [docker, ubuntu]
---

Follow the steps to install Docker Community Edition:

{% highlight shell %}
$ sudo apt install curl software-properties-common
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
$ sudo apt update
$ sudo apt install docker-ce
$ sudo usermod -aG docker $(id -un)  # optional, allows you to run docker without `sudo`
{% endhighlight %}
