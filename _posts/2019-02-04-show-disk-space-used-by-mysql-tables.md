---
layout: post
title: How to show the disk space used by MySQL tables?
date: 2019-02-04
categories: [database]
tags: [mysql]
---

Connect to your MySQL server and run the following:

{% highlight sql %}
mysql> select table_name, round(((data_length + index_length) / (1024 * 1024)), 2) as `Size in MB` from information_schema.tables where table_schema = 'YOUR_DB_NAME' order by `Size in MB`;
{% endhighlight %}
