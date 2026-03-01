---
layout: post
title: How to install Docker on Amazon Linux 2?
date: 2020-07-22
categories: [aws, containers]
tags: [docker]
---

SSH to your Amazon Linux 2 EC2 instance, and follow the steps:

{% highlight shell %}
$ sudo amazon-linux-extras install docker
$ sudo systemctl enable docker
$ sudo systemctl start docker
$ sudo usermod -aG docker ec2-user  # Optional, allows you to run `docker` commands without `sudo`
$ sudo reboot
{% endhighlight %}

That's it!
