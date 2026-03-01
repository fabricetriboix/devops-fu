---
layout: post
title: How to fix login issues with a password-less root MySQL user?
date: 2018-05-22
categories: [database]
tags: [mysql]
---

If you installed MySQL without a root password, you will have
difficulties to login as root. For example, you need to sudo first,
and can’t login as a regular user. The solution to this problem is to
create a new root-like user like so:

{% highlight sql %}
CREATE USER admin@localhost IDENTIFIED BY 'SOME_PASSWORD';
GRANT ALL PRIVILEGES ON *.* TO admin@localhost WITH GRANT OPTION;
{% endhighlight %}

To allow this new user to login from another host, repeat the 2
commands above, replacing `localhost` with `%`. Please note that this
is not recommended for security reasons.
