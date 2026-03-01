---
layout: post
title: How to enable and analyse the slow query log on MySQL?
date: 2018-10-02
categories: [database]
tags: [mysql]
---

Set the following variables in your MySQL config file:

{% highlight conf %}
slow_query_log = 1 (means true)
long_query_time = 0 (means 0 seconds)
log_queries_not_using_indexes = 1 (means true)
log_output = FILE (default is TABLE)
{% endhighlight %}

Setting `long_query_time` to 0 will instruct MySQL to log _all_
queries to the slow query log. Some queries might not be that slow,
but if repeated often enough will have a significant impact on the
performance of your MySQL database. Setting `long_query_time` to 0
will allow you to catch that kind of scenario as well. Let MySQL run
for a while, then download
[pt-query-digest](https://percona.com/get/pt-query-digest), and run it
like so:

{% highlight shell %}
$ pt-query-digest slow-query.log > slow-query.log.out
{% endhighlight %}

Look at the `slow-query.log.out` file to find out which queries are
slowing down your MySQL database.
