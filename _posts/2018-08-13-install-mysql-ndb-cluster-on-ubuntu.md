---
layout: post
title: How to install a MySQL NDB Cluster on Ubuntu?
date: 2018-08-13
categories: [database]
tags: [mysql]
---

Introduction
============

Official documentation
[here](https://dev.mysql.com/doc/refman/5.7/en/mysql-cluster.html).

There are 3 different types of nodes for MySQL Cluster: NDB manager,
NDB node and MySQL server. Please note the standard MySQL server does
not support NDB tables, so the MySQL server must be the one compiled
with NDB support. This page assumes the following configuration:

 - Only one NDB manager node is running, which is enough for a small
   setup like this. Please note the NDB manager does not have to be
   present for the cluster to work. It is used essentially once at
   startup, and thereafter when a node dies for some reason.
 - Only one MySQL server is running, and on the same VM as the NDB
   manager. Having a single MySQL server is a single point of failure,
   but that’s good enough for now. It is possible to add more MySQL
   servers for redundancy and load balancing. Ideally, the MySQL
   servers should run on their own VMs.
 - Two NDB data nodes; that’s the minimum required to get an NDB
   cluster up and running. This will provide fault tolerance (in the
   sense that if one node dies, the cluster itself will continue
   running), but will not provide performance improvements.

APT repository
==============

MySQL conveniently provides an apt repository for Ubuntu 14.04 and
16.04. Follow these steps on all three VMs:

 - Go to [http://dev.mysql.com/downloads/repo/apt/](http://dev.mysql.com/downloads/repo/apt/)
 - Download the relevant `mysql-apt-config` .deb file
 - Install it like so: `$ sudo dpkg -i mysql-apt-config*.deb`
 - Select "MySQL Server & Cluster"
 - Select "mysql-cluster-7.5" (the latest GA release at the time of writing)
 - Select "OK" in the scroll down list
 - Run `$ sudo apt update`

Installing and configuring the NDB manager node
===============================================

Provided you installed mysql-apt-config with the right options, just
run:

{% highlight shell %}
$ sudo apt install mysql-cluster-community-management-server
{% endhighlight %}

Then create a directory `/var/lib/mysql-cluster,` and create a text
file in that directory named `config.ini`. Edit this file so the
content looks like this (replace the IP addresses with the real ones):

{% highlight ini %}
[ndb_mgmd]
HostName=10.132.156.195
DataDir=/var/lib/mysql-cluster

[ndbd]
HostName=10.132.156.202
NodeId=2
DataDir=/usr/local/mysql/data

[ndbd]
HostName=10.132.156.217
NodeId=3
DataDir=/usr/local/mysql/data

[mysqld]
HostName=10.132.156.195
{% endhighlight %}

You should be able to run the NDB manager like so (replace the IP
address with the local private IP address):

{% highlight shell %}
$ sudo ndb_mgmd -f /var/lib/mysql-cluster/config.ini --bind-address=10.132.156.195
{% endhighlight %}

**Important security note**: If your VM is publicly accessibly, please
make sure you use the `--bind-address` argument to forces the NDB
manager to bind to the private IP address in order to prevent anyone
from the outside world to connect to the NDB manager.

In order to get the NDB manager to start automatically, the simplest
is to add it to `rc.local`. Edit the `/etc/rc.local` file and add the
line `ndb_mgmd -f /var/lib/mysql-cluster/config.ini
--bind-address=10.132.156.195` (before the exit 0 line if there is
one). Reboot the machine and run `ps -ef`, you should see the
`ndb_mgmd` process running.

There is also a command-line utility to access the NDB manager
(replace the IP address with the local private IP address):

{% highlight shell %}
$ sudo apt install mysql-cluster-community-client
$ sudo ndb_mgm 10.132.156.195
ndb_mgm> show
ndb_mgm> exit
{% endhighlight %}

NDB data nodes
==============

This should be repeated for each data node.

Provided you installed mysql-apt-config with the right options, just
run:

{% highlight shell %}
$ sudo apt install mysql-cluster-community-data-node
{% endhighlight %}

Edit the file `/etc/my.cnf` (create it if necessary), and make it look
like so:

{% highlight ini %}
[mysql_cluster]
ndb-connectstring=10.132.156.195
{% endhighlight %}

Replace the IP address with the private IP address of the VM on which
the NDB manager is installed. You will also need to create the data
directory: `$ sudo mkdir -p /usr/local/mysql/data`. Start the NDB data
node daemon like so (replace the IP address with the local private IP
address):

{% highlight shell %}
$ sudo ndbd --bind-address=10.132.156.202`
{% endhighlight %}

**Important security note**: The `--bind-address` argument forces the
NDB manager to bind to the specified address. In our case, we ensure
it binds to the private IP only, which prevents anyone from the
outside world to connect to the NDB data node.

In order to get the NDB data node to start automatically, the simplest
is to add it to `rc.local`. Edit the `/etc/rc.local` file and add the
line `ndbd --bind-address=10.132.156.202` (before the exit 0 line if
there is one). Reboot the machine and run `ps -ef`, you should see the
`ndbd` process running.

_NB_: If the machine the NDB data node is running on has 2 or more
CPUs, use `ndbmtd` instead of `ndbd` for better performances. This is
the multi-threaded version of ndbd. Please note that you still need to
enable multi-threading in the config file. Read the documentation for
`ndbmtd` for more information.

MySQL server
============

This should be repeated for each MySQL server, if applicable.

Provided you installed mysql-apt-config with the right options, just
run:

{% highlight shell %}
$ sudo apt install mysql-cluster-community-server
{% endhighlight %}

Edit the file `/etc/mysql/mysql.conf.d/mysqld.cnf` and do the
following under the `[mysqld]` section:

 - Set the bind-address parameter to the private IP address of this
   local machine
 - Add the keyword `ndbcluster`
 - Add a section `[mysql_cluster]` if it does not exist
 - Under the section `[mysql_cluster]`, set the `ndb-connectstring`
   parameter to the private IP address of the NDB manager node (in our
   example, the MySQL server and the NDB manager both runs on the same
   machine)

The file should look similar to this:

{% highlight ini %}
[mysqld]
...
bind-address = 10.132.156.195
ndbcluster

[mysql_cluster]
ndb-connectstring = 10.132.156.195
{% endhighlight %}

**Important security note**: The `bind-address` option forces the
MySQL server to bind to the specified address. In our case, we ensure
it binds to the private IP only, which prevents anyone from the
outside world to connect to the MySQL server.

Restart the MySQL server so it can pick up the new configuration:
`sudo systemctl restart mysql` You can check everything looks good by
starting `ndb_mgm` and issuing the command show. You should see that
both data nodes and the MySQL server are connected.

**DEPRECATED**: Download MySQL Cluster from
[there](http://dev.mysql.com/downloads/cluster/) (select the bundle
.tar file).
