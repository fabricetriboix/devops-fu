---
layout: post
title: How to set up MFA on AWS?
date: 2022-05-25
categories: [aws]
tags: []
---

In this article, we will see how to enable MFA for a user, and how to
enforce MFA for both the console and the command line.

How to setup MFA for an IAM user
================================

Follow the steps:

 - Install FreeOTP, Google Authenticator or similar on your phone
 - Login to the AWS UI:
   [https://console.aws.amazon.com](https://console.aws.amazon.com)
 - Select the IAM service
 - Click on "Users" in the left pane
 - Click on the user you want to modify in the middle pane
 - Click on the "Security credentials" tab
 - Next to the "Assigned MFA device" click on "Manage"
 - Select "Virtual MFA device" and click on "Continue"
 - Click on "Show QR code" and scan it with your chosen app on your phone
 - Your app should show a 6 digit code, enter it in the field "MFA code 1"
 - Wait for a little while until the app shows another code
 - Enter that 2nd code in the field "MFA code 2"
 - Click on "Assign MFA"

How to enforce MFA
==================

This section will detail how to ensure that users must go through MFA
before they can do anything in your AWS account.

Create a role
-------------

For the sake of example, I will create an IAM role with administrator
access. This is the role that users will assume after logging in. This
role can be assumed only if the user has MFA enabled and passed the
MFA step.

Create a role with the following trust relationship:
{% highlight json %}
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::332124921534:root"
            },
            "Action": "sts:AssumeRole",
            "Condition": {
                "Bool": {
                    "aws:MultiFactorAuthPresent": "true"
                }
            }
        }
    ]
}
{% endhighlight %}

To create such a role in the AWS console, follow these steps:

 - Navigate to the IAM console
 - Click on "Roles" in the left pane
 - Click on "Create role"
 - For the trusted entity type, select "AWS account"
 - Then select "This account"
 - Tick the box "Require MFA"
 - Click "Next"
 - For the policy, select "AdministratorAccess", or some more
   restrictive policies if that's your goal
 - Click "Next"
 - Give the role a name (eg: `AdminRole`) and click "Create role"

Create a policy
---------------

The next step is to create a policy to assume the above role. The
policy document should look like this:

{% highlight json %}
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": "arn:aws:iam::ACCOUNT_NUMBER:role/AdminRole"
        }
    ]
}
{% endhighlight %}

 - In the IAM console, click on "Policies" in the left pane
 - Click on "Create policy"
 - Enter a JSON policy that looks like the one above (replace the
   values for your circumstances)
 - Click on "Next: Tags"
 - Add tags if you want
 - Click on "Next: Review"
 - Give the policy a name (eg: `AssumeAdminPolicy`) and click "Create
   policy"

Create a user group
-------------------

The next step is to create a group of users who will be able to assume
the role thanks to the policy:

 - In the IAM console, click on "User groups" in the left pane
 - Click on "Create group"
 - Give a name to the groups (eg: `Admins`)
 - Add users to the group if you want (you can always do that later)
 - Attach the `AssumeAdminPolicy`
 - Click on "Create group"

You should ensure that users do not get additional permissions from
being member of other groups or policies directly attached to them,
otherwise that will defeat the purpose of mandating MFA in the first
place.

How to login on the AWS console
-------------------------------

To login to the AWS console, point your browser to
[https://console.aws.amazon.com/](https://console.aws.amazon.com/) and
enter the required details. The next screen will ask you for your MFA.

Now you are logged in but you should pretty much be unable to do
anything. Click on your name in the top-right corner and click on
"switch role". For the "Account" field, enter the same id as the
account you just logged into. For the "Role" field, enter `AdminRole`
(or whatever name you chose for the role). You can choose a display
name and colour. Click on "Switch Role". That's it!

Next time you log in, when you click on your name in the top-right
corner, you will see a section "role history" where you can just click
directly on the display name you chose in the previous step.

How to login with the AWS cli
-----------------------------

Edit the `~/.aws/credentials` file to add your access key id and
secret access key. It should look like this:

```ini
[base]
aws_access_key_id = AKIAxxxx
aws_secret_access_key = xxxxxxx
```

Now edit your `~/.aws/config` file so it looks like this:

```ini
[profile base]
region = PREFERRED_REGION
output = json
mfa_serial =  arn:aws:iam::ACCOUNT_NUMBER:mfa/USERNAME

[profile main]
source_profile = base
role_arn = arn:aws:iam::ACCOUNT_NUMBER:role/AdminRole
```

The next time you run the aws cli, it will ask you for your MFA.
