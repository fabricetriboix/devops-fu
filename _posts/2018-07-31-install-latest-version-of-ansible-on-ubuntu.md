---
layout: post
title: How to install the latest version of Ansible on Ubuntu?
date: 2018-07-31
categories: [sysadmin]
tags: [ansible, ubuntu]
---

From
[this](http://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#latest-releases-via-apt-ubuntu),
the steps are:

{% highlight shell %}
$ sudo apt update
$ sudo apt install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt install ansible
{% endhighlight %}
