---
layout: post
title: How to setup MFA on AWS using only the command line?
date: 2019-05-07
categories: [aws]
tags:
---

You obviously need to have the credentials to perform IAM operations
(either you have the access key and secret key of a such a user, or
you can SSH to an EC2 instance whose role allow that, etc.) Also, this
article is about virtual MFA devices (typically using Google
Authenticator); for hardware devices, you might need to change a few
things. If you don't like Google Authenticator, you can use FreeOTP
instead.

If you are running the commands from an EC2 instance with an
associated role allowing IAM access, don’t include the `--profile
MYPROFILE` part of the commands because you don’t need an access key
and a secret key. Otherwise, you need an access key and a secret key
and I strongly recommend you segregate them in "profiles", and use the
`--profile MYPROFILE` argument on the command line every single time.

The reason to keep this segregation is so that you don’t mess up with
an AWS account while thinking you’re accessing another AWS account; by
forcing yourself to type `--profile MYPROFILE` every time, you are
almost guaranteed that this will never happen. For the same reason, I
don’t recommend setting the `AWS_PROFILE` environment variable, as
sooner or later you will mess up with an AWS account thinking you are
targeting another AWS account.

Assuming you have the necessary permissions, follow along.

If the user you are targeting (which may be yourself) already has an
MFA device associated with it, you need to de-associate it first
because a user can only be associated with one MFA device at a time:

{% highlight shell %}
$ aws --profile MYPROFILE iam deactivate-mfa-device --user-name USERNAME --serial-number arn:aws:iam::123456789012:mfa/DEVICENAME
$ aws --profile MYPROFILE iam delete-virtual-mfa-device --serial-number arn:aws:iam::123456789012:mfa/DEVICENAME
{% endhighlight %}

It is good housekeeping to delete the MFA device and to keep one MFA
device per user (don’t re-use MFA devices across users).

Now create a virtual MFA device:

{% highlight shell %}
$ aws --profile MYPROFILE iam create-virtual-mfa-device --virtual-mfa-device-name DEVICENAME --outfile my-qr-code.png --bootstrap-method QRCodePNG
{% endhighlight %}

It is good practice to use the USERNAME as the DEVICENAME. This way,
it is a lot easier to identify that this virtual MFA device is to be
associated with that user. Keep the `my-qr-code.png` file safe, it is
a secret. Now open the QR code with your favourite image viewer,
install Google Authenticator on your mobile phone and scan the QR
code. Google Authenticator will start churning out time-based one-time
passwords (TOTPs) every 30 seconds.

Finally, associate the newly created virtual MFA device with the user:

{% highlight shell %}
$ aws --profile MYPROFILE iam enable-mfa-device --user-name USERNAME --serial-number arn:aws:iam::123456789012:mfa/DEVICENAME --authentication-code1 CODE1 --authentication-code2 CODE2
{% endhighlight %}

Replace CODE1 by the TOTP Google Authenticator is currently showing
you. Then wait for the next code to show up, and this is your CODE2.
Press enter to send the command before the 2nd code disappears
otherwise the association will fail.

If you ever lose your phone for whatever reason, you can use the QR
code to restore your association using the `aws iam resync-mfa-device`
command without having to delete the original MFA device and re-create
a new one.
