---
layout: post
title: How to resize the root partition of CentOS running on an EC2 instance?
date: 2020-03-20
categories: [sysadmin, aws]
tags: []
---

You can increase the size of the root EBS volume, but how to make
CentOS reflect that change?

SSH into the CentOS instance and run the following:

{% highlight shell %}
$ sudo yum install cloud-utils-growpart
$ sudo growpart /dev/nvme0n1 1
$ sudo reboot
{% endhighlight %}

After the reboot, the root partition would have been resized. Tada!
