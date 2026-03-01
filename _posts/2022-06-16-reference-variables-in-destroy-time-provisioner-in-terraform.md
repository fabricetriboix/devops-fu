---
layout: post
title: How to reference variables in destroy-time provisioner in Terraform?
date: 2022-06-16
categories: [terraform]
tags: []
---

Are you using a local-exec provisioner to run upon destruction with a
clause `when = destroy` and getting this error message:

> Destroy-time provisioners and their connection configurations may
> only reference attributes of the related resource, via 'self',
> 'count.index', or 'each.key'.

A workaround is to use triggers, so your code would like this:

{% highlight conf %}
resource "null_pointer" {
  triggers = {
    local_var = var.external_var
  }

  provisioner "local-exec" {
    when = destroy
    command = "echo $self.triggers.local_var"
  }
}
{% endhighlight %}

Instead of this:

{% highlight conf %}
resource "null_pointer" {
  provisioner "local-exec" {
    when = destroy
    command = "echo $var.external_var"
  }
}
{% endhighlight %}
