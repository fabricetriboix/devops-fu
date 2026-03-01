---
layout: post
title: How to make an S3 bucket public?
date: 2020-04-13
categories: [aws]
tags: []
---

The first thing is to make sure the "Public Access Block" are
disabled. In the AWS console, go to your bucket, then "Permissions",
then "Block public access".

Then set the bucket policy to this:

{% highlight json %}
{
    "Version": "2008-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::MYBUCKET/*"
        }
    ]
}
{% endhighlight %}
