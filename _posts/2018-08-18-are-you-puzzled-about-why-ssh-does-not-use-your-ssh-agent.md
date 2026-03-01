---
layout: post
title: Are you puzzled about why SSH does not use your ssh-agent?
date: 2018-08-18
categories: [sysadmin]
tags: [ssh]
---

It could be that you are using the `IdentityFile` combined with the
`IdentitiesOnly` option. When `IdentitiesOnly` is set to `yes`, ssh
will not try to use the ssh agent, but only the key that you specified
with `IdentityFile`. Consequently, you have to enter the passphrase
every time. Instead, use the `CertificateFile` option (and specify the
corresponding public key). This way, ssh will use the ssh agent
properly. Please note that in that case, you will need to load the
private key in the ssh-agent first, eg: `$ ssh-add
/PATH/TO/PRIVATE/KEY`. If you don’t, ssh will not find the private key
in the ssh agent, and has no real way to know what is the private key
to use.

**Update 26th March 2019**: I now use a different method because even
the previous one doesn’t work all the time (for example with ssh
forwarding). I just put all (or most) my ssh keys outside the `~/.ssh`
directory (eg: `~/.ssh/keys`), and load them using ssh-agent as and
when needed, and remove/clean up the ssh agent when there are too
many.
