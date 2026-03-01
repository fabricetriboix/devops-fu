---
layout: post
title: How to install and configure OpenVPN on Amazon Linux?
date: 2019-03-25
categories: [sysadmin]
tags: [openvpn, vpn]
---

**Important note**: What I wrote here will not work on Amazon Linux 2;
only on Amazon Linux.

There are some rare tutorials on the net, like [this outdated
one](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-openvpn-server-on-ubuntu-14-04).
Save yourself some headaches and use the following script to easily
install and configure OpenVPN server, and easily generate all the
keys, certificate and configuration files required for clients.

Nyr wrote a very good script to install and configure OpenVPN. I just
did some modifications to make it run on Amazon Linux, and also to ask
for a passphrase when generating client keys as I think this is quite
important.

Run the following on the machine you want to use as your OpenVPN
server:

{% highlight shell %}
$ wget https://raw.githubusercontent.com/fabricetriboix/openvpn-install/master/manage-openvpn.sh
$ chmod +x manage-openvpn.sh
$ sudo ./manage-openvpn.sh -i
{% endhighlight %}

And following the instructions.

Also of interest: you can use a NAT instance to setup your OpenVPN
server. So you can use a small instance to do both NAT and VPN, which
is much cheaper than using NAT Gateways + AWS VPN solutions, provided
you are happy with the limits of having a single instance running both
NAT and VPN.

To add a user:

{% highlight shell %}
$ sudo ./manage-openvpn.sh -a NEWUSER
{% endhighlight %}

Use the generated `NEWUSER.ovpn` unified configuration file to connect
your client to your OpenVPN server. This file contains secrets, so you
must handle it carefully. For example on a Linux laptop with OpenVPN
installed:

{% highlight shell %}
$ sudo openvpn NEWUSER.ovpn
{% endhighlight %}

Please note the above script should work on Debian, Ubuntu, RedHat,
CentOS and Amazon Linux (maybe even on Fedora too).
