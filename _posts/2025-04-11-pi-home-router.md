---
layout: post
title: How to transform a Raspberry Pi into a secure home wifi router?
date: 2025-04-11
categories: []
tags: []
---

Thanks a lot for NetworkChuck, this blog post is derived from [this
video)[https://www.youtube.com/watch?v=jlHWnKVpygw].

The first thing is to gather the hardware:
  - A Raspberry Pi, version 3 or more, with a power supply. I
    personally dusted an old Raspberry Pi 3B+, but if I had to start
    from scratch, I would buy the newest and shiniest.
  - An ethernet cable (cross-over or not should not make a difference
    these days).
  - A wifi dongle (I used a TP-Link Archer T2U).
  - A micro SD card. I used a old, dusty 8GB one.

[Download OpenWRT](https://openwrt.org/toh/views/toh_fwdownload) and
flash it to the micro SD card. I recommend [Raspberry Pi
Imager](https://www.raspberrypi.com/news/raspberry-pi-imager-imaging-utility/)
to do that (use the "Use custom" option and select the OpenWRT image
file you downloaded; do not apply any customisation).

Connect the Ethernet cable between the Raspberry Pi and your computer,
and power the Raspberry Pi on. Configure your computer's wired
connection to use a fixed IP address of `192.168.1.10/255.255.255.0`
with a default gateway of `192.168.1.1` (which is the IP address used
by OpenWRT by default). Wait for a minute or two for OpenWRT to boot
up, and you should be able to SSH into OpenWRT like so:

{% highlight shell %}
$ ssh root@192.168.1.1
{% endhighlight %}

OpenWRT should just let you in because initially `root` doesn't have a
password. First thing is to change the password:

{% highlight shell %}
$ passwd
{% endhighlight %}

Next step is to change some configuration files. Make sure you back
them up before making changes. The first file to change is
`/etc/config/network` and it should look like this (feel free to
change the private subnet and the DNS servers):

{% highlight config %}
config interface 'loopback'
	option device 'lo'
	option proto 'static'
	option ipaddr '127.0.0.1'
	option netmask '255.0.0.0'

config globals 'globals'
	option ula_prefix 'fd47:1572:b63::/48'

config device
	option name 'br-lan'
	option type 'bridge'
	list ports 'eth0'

config interface 'lan'
	option device 'br-lan'
	option proto 'static'
	option ipaddr '10.217.58.1'
	option netmask '255.255.255.0'
	option ip6assign '60'
	option force_link '1'

config interface 'wwan'
	option proto 'dhcp'
	option peerdns '0'
	option dns '9.9.9.9 1.1.1.1'

config interface 'vpnclient'
	option ifname 'tun0'
	option proto 'none'
{% endhighlight %}

Next, modify the `/etc/config/firewall` file to change the config zone
`wan` to accept inputs. The line should read `option input ACCEPT`.
The file should like this once modified:

{% highlight config %}
config defaults
	option syn_flood '1'
	option input 'REJECT'
	option output 'ACCEPT'
	option forward 'REJECT'

config zone
	option name 'lan'
	option input 'ACCEPT'
	option output 'ACCEPT'
	option forward 'ACCEPT'
	list network 'lan'

config zone
	option name 'wan'
	option input 'ACCEPT'
	option output 'ACCEPT'
	option forward 'REJECT'
	option masq '1'
	option mtu_fix '1'
	list network 'wan'
	list network 'wan6'
	list network 'wwan'

config forwarding
	option src 'lan'
	option dest 'wan'

config rule
	option name 'Allow-DHCP-Renew'
	option src 'wan'
	option proto 'udp'
	option dest_port '68'
	option target 'ACCEPT'
	option family 'ipv4'

config rule
	option name 'Allow-Ping'
	option src 'wan'
	option proto 'icmp'
	option icmp_type 'echo-request'
	option family 'ipv4'
	option target 'ACCEPT'

config rule
	option name 'Allow-IGMP'
	option src 'wan'
	option proto 'igmp'
	option family 'ipv4'
	option target 'ACCEPT'

config rule
	option name 'Allow-DHCPv6'
	option src 'wan'
	option proto 'udp'
	option dest_port '546'
	option family 'ipv6'
	option target 'ACCEPT'

config rule
	option name 'Allow-MLD'
	option src 'wan'
	option proto 'icmp'
	option src_ip 'fe80::/10'
	list icmp_type '130/0'
	list icmp_type '131/0'
	list icmp_type '132/0'
	list icmp_type '143/0'
	option family 'ipv6'
	option target 'ACCEPT'

config rule
	option name 'Allow-ICMPv6-Input'
	option src 'wan'
	option proto 'icmp'
	list icmp_type 'echo-request'
	list icmp_type 'echo-reply'
	list icmp_type 'destination-unreachable'
	list icmp_type 'packet-too-big'
	list icmp_type 'time-exceeded'
	list icmp_type 'bad-header'
	list icmp_type 'unknown-header-type'
	list icmp_type 'router-solicitation'
	list icmp_type 'neighbour-solicitation'
	list icmp_type 'router-advertisement'
	list icmp_type 'neighbour-advertisement'
	option limit '1000/sec'
	option family 'ipv6'
	option target 'ACCEPT'

config rule
	option name 'Allow-ICMPv6-Forward'
	option src 'wan'
	option dest '*'
	option proto 'icmp'
	list icmp_type 'echo-request'
	list icmp_type 'echo-reply'
	list icmp_type 'destination-unreachable'
	list icmp_type 'packet-too-big'
	list icmp_type 'time-exceeded'
	list icmp_type 'bad-header'
	list icmp_type 'unknown-header-type'
	option limit '1000/sec'
	option family 'ipv6'
	option target 'ACCEPT'

config rule
	option name 'Allow-IPSec-ESP'
	option src 'wan'
	option dest 'lan'
	option proto 'esp'
	option target 'ACCEPT'

config rule
	option name 'Allow-ISAKMP'
	option src 'wan'
	option dest 'lan'
	option dest_port '500'
	option proto 'udp'
	option target 'ACCEPT'
{% endhighlight %}

Now reboot the Raspberry Pi. You will also need to change the
configuration of your wired interface to be provisioned by DHCP. After
a couple of minutes, you should be able to SSH again into your
Raspberry Pi, but this time using the IP address you set in the `lan`
interface of the `/etc/config/network` file.

The next step of the video is to connect to the OpenWRT web UI through
the Ethernet cable, but it was not working for me. I flashed a
snapshot image of OpenWRT which does not include LuCI, OpenWRT's web
UI. There are instructions
[here](https://openwrt.org/docs/guide-user/luci/luci.essentials#tab__for_releases_newer_than_2410_and_snapshots)
to install LuCI, but it requires an internet connection. So I
configured the onboard Raspberry Pi wifi interface using the command
line (something that is done using OpenWRT's web UI in the video).
This is done by modifying the `/etc/config/wireless` file so it looks
like this (obviously, you will need to change your SSID and password
according to your wifi):

{% highlight config %}
config wifi-device 'radio0'
	option type 'mac80211'
	option path 'platform/soc/3f300000.mmcnr/mmc_host/mmc1/mmc1:0001/mmc1:0001:1'
	option band '5g'
	option channel '1'
	option htmode 'HT20'
	option disabled '0'
	option hwmode '11g'
	option short_gi_40 '0'
	option cell_density '0'

config wifi-iface 'wifinet1'
	option device 'radio0'
	option mode 'sta'
	option network 'wwan'
	option ssid 'REDACTED'
	option encryption 'psk2'
	option key 'REDACTED'
{% endhighlight %}

_NB: Some of these changes are similar to what is described in the
video, so if you follow the video, you can skip this part or just
double-check that it matches._

Then run the following commands in your OpenWRT:

{% highlight shell %}
uci commit wireless
wifi
ping www.yahoo.com
{% endhighlight %}

Once I got that up and running, I was able to [install
LuCI](https://openwrt.org/docs/guide-user/luci/luci.essentials#tab__for_releases_newer_than_2410_and_snapshots).
Now you should be able to direct your browser to http://10.217.58.1 to
access the LuCI web UI.

Then run the following on your Raspberry Pi:

{% highlight shell %}
apk update
apk upgrade
apk add kmod-rt2800-lib kmod-rt2800-usb kmod-rt2x00-lib kmod-rt2x00-usb kmod-usb-core kmod-usb-uhci kmod-usb-ohci kmod-usb2 usbutils openvpn-openssl luci-app-openvpn nano
{% endhighlight %}
