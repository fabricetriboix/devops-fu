---
layout: post
title: How to disable IPv6 on Linux?
date: 2024-05-28
categories: [sysadmin]
tags: [ubuntu]
---

These are the steps for Grub. Edit the `/etc/default/grub` file and
locate the `GRUB_CMDLINE_LINUX` variable. Add `ipv6.disable=1` to the
value of this variable. Now run `$ sudo update-grub` and reboot your
computer.
