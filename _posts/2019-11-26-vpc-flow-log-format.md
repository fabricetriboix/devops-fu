---
layout: post
title: What is the VPC flow log format?
date: 2019-11-26
categories: [aws]
tags: [vpc]
---

When you enable VPC flow logs, records will be created in CloudWatch
Logs (or S3 if you choose so, but CloudWatch Logs is definitely better
for debugging). There will be one log stream per network interface,
and the format of the logs is like so:

{% highlight conf %}
version account-id interface-id srcaddr dstaddr srcport dstport protocol packets bytes start end action log-status
{% endhighlight %}

`action` will usually by ACCEPT or REJECT.

Check out the [AWS
documentation](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html#flow-log-records)
for more information.
