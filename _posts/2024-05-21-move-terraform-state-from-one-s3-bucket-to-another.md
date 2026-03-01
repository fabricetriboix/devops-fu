---
layout: post
title: How to move a Terraform state from one S3 bucket to another?
date: 2024-05-21
categories: [terraform]
tags: []
---

Steps:


{% highlight shell %}
$ terraform init
$ terraform state pull > terraform.tfstate
$ aws s3 cp --sse AES256 terraform.tfstate s3://NEWBUCKET/NEWPATH
{% endhighlight %}

Now update the terraform backend to point to the new S3 bucket. Then run:

{% highlight shell %}
$ terraform init -reconfigure
{% endhighlight %}
