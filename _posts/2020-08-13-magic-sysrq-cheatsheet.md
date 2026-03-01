---
layout: post
title: Magic SysRq cheatsheet
date: 2020-08-13
categories: [sysadmin]
tags: [linux]
---

First of all, ensure Magic SysRq is enabled:

{% highlight shell %}
$ cat /proc/sys/kernel/sysrq
{% endhighlight %}

0 means "disabled", 1 means "everything enabled" and >1 is a bitmask
of allowed keys. If you are using Ubuntu, you can modify the
`/etc/sysctl.d/10-magic-sysrq.conf` file to set this value at boot
time.

You invoke SysRq by hitting "Alt + SysRq + Key" on your keyboard. If
you can’t find the "SysRq" key on your keyboard, it is usually the
same as the "Print Screen" key.

If your Linux computer is acting crazy and you want to safely reboot
it, this is the mnemonic to remember: "Raising Elephants Is So Utterly
Boring":

 - R: unRaw (take control of the keyboard back from X)
 - E: tErminate (send SIGTERM to all processes)
 - I: kIll (send SIGKILL to all processes except init)
 - S: Sync (flush filesystem buffers)
 - U: Unmount (remount all filesystems read-only)
 - B: reBoot

Another good key to keep in mind is `f`. This calls `oom_kill()` which
finds and kills a "big" process in order to give back some RAM to your
system. If your system is slowing to a crawl, this is something that
can save your skin.

More information
[here](https://en.wikipedia.org/wiki/Magic_SysRq_key).
