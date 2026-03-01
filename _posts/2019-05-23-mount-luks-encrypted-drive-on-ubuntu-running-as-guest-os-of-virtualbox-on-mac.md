---
layout: post
title: How to mount a LUKS encrypted drive on Ubuntu running as a Guest OS of VirtualBox on Mac?
date: 2019-05-23
categories: [sysadmin]
tags: [macos, ubuntu, virtualbox]
---

Insert the encrypted device in your Mac. It will tell you it can't use
it and would invite you to initialize it. Just click "Ignore". Run the
disk utility to identify what disk is your encrypted device (probably
it will be "disk2").

Then open a terminal and run the following:

{% highlight shell %}
$ sudo VBoxManage internalcommands createrawvmdk -filename mydevice.vmdk -rawdisk /dev/disk2
{% endhighlight %}

Replace "disk2" by the actual disk for you.

**WARNING** Don't get this wrong or you can corrupt your Mac's hard
drive!

In VirtualBox, select your VM and click "Settings". Then click
"Storage" and "Controller: SATA". Then click on the "plus" icon next
to "Controller: SATA" (either optical drive or hard disk). Then click
"Choose existing disk". Click the "Add" button and locate the
"mydevice.vmdk" file. Ensure it is selected and click "Choose". Then
start your Ubuntu VM and `lsblk` should see the new device now.

For some reason, Nautilus doesn’t show it, so I had to set it up manually:

{% highlight shell %}
$ sudo lsblk  # Locate your encrypted partition
$ sudo cryptsetup open /dev/sdb1 mydevice  # Replace sdb1 by what you identified in lsblk
$ sudo mount /dev/mapper/mydevice /mnt
{% endhighlight %}
