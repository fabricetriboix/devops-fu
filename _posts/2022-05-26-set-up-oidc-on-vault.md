---
layout: post
title: How to set up OIDC on Vault?
date: 2022-05-26
categories: [vault]
tags: []
---

Just for the sake of clarity, we are talking about Hashicorp Vault
here.

Create a policy
---------------

The first step is to write a policy for the users that will login
through OIDC. A good guide on Vault policies can be found
[here](https://github.com/jeffsanicola/vault-policy-guide). Let's
assume you wrote your policy document in a file named
`mypolicy.hcl`, you would then run this:

{% highlight shell %}
$ vault policy write mypolicy /path/to/mypolicy.hcl
{% endhighlight %}

If you want to be sure, you can read it back:
{% highlight shell %}
$ vault read sys/policy/mypolicy
{% endhighlight %}

To list the existing policies:

{% highlight shell %}
$ vault read sys/policy
{% endhighlight %}

Enable OIDC
-----------

The next step is to enable the OIDC authentication method:

{% highlight shell %}
$ vault auth enable -path=MYPATH oidc
{% endhighlight %}

The `-path` argument is optional. By default, the OIDC method is
mounted at `oidc/`, but you can use the `-path` argument to change
that. If you do choose a different mount path, you can tune the UI to
make it more visible:

{% highlight shell %}
$ vault auth tune -listing-visibility=unauth MYPATH
{% endhighlight %}

If you want to verify that your command worked, you can list the auth
methods like so:

{% highlight shell %}
$ vault auth list
{% endhighlight %}

Make a note of the "mount accessor", you will need it later. When you
run `vault auth list`, the third column is the mount accessor, and it
looks like `auth_oidc_a1b2c3d4`.

Configure OIDC
--------------

The next step is to configure OIDC. This requires the OIDC client id,
the OIDC client secret and the discovery URL from the OIDC provider.

{% highlight shell %}
$ vault write auth/MYPATH/config oidc_discovery_url=XYZ oidc_client_id=XYZ oidc_client_secret=XYZ default_role=MYROLE
{% endhighlight %}

We now need to create the role MYROLE for the OIDC auth method. 

Create OIDC role
----------------

To create the OIDC role, you will need to know the allowed redirect
URLs, as well as some details 

{% highlight shell %}
$ vault write auth/MYPATH/role/default role_type=oidc allowed_redirect_uris="URL1,URL2,..." oidc_scopes=profile user_claim=email groups_claim=groups policies=default verbose_oidc_logging=true
{% endhighlight %}

The `verbose_oidc_logging=true` parameter is useful for debugging when
setting things up. Please note that the overall Vault log level must
be set to `debug` for this parameter to take effect. You should remove
this parameter when deploying to production.

Your `user_claim`, `groups_claim`, etc. will vary. Maybe you will need
to add more claims, it all depends on your setup. Setting the policy
to `default` is probably a good idea, because all users that will
login via OIDC will be assigned this policy. Fine-tuning in terms of
what users can access or not can be done by assigning additional
policies. In this article, we will assign additional policies through
groups. So an "admin" group can have very wide permissions, while a
"dev" group would have a narrower set of permissions.

Create the group
----------------

Now let's create a group:

{% highlight shell %}
$ vault write identity/group name=myteam policies=mypolicy type=external
{% endhighlight %}

In the output, you will find the group id. Make a note of it, you will
need it later. Please note the following:
 - The group is of type "external", because we want to be able to use
   it for users that login through OIDC
 - The group name is "myteam", which can be different from the group
   name on the OIDC provider
 - We assigned the policy we created earlier

We now need to link this Vault group with the OIDC group:

{% highlight shell %}
$ vault write identity/group-alias name=theteam canonical_id=GROUP_ID mount_accessor=MOUNT_ACCESSOR
{% endhighlight %}

Some notes:
 - The `GROUP_ID` is the group id you noted down earlier
 - The 'MOUNT_ACCESSOR` is the mount access you noted down earlier
 - The group name `theteam` is the OIDC group name, which will be set
   as the value of the claim identified in the `groups_claim`
   parameter when you created the OIDC role

Testing it
----------

Point your browser to your Vault UI. Select the `OIDC` authentication
method. If you chose a non-standard mount path for the OIDC auth
method, you will need to click on "More options" and set the path
there. If you tuned the visibility of the OIDC auth method, you should
be able to see the non-standard mount path there.

Click on "Sign in with OIDC provider". A pop-up will appear for you to
authenticate with the OIDC provider, and give permission to Vault to
use your account. If everything went well, you should now be logged
in.

If on the OIDC provider you are a member of the `theteam` group, you
should now have the policy `mypolicy` attached to your token. You can
check your token like so. Click on the human figure in the top right
corner and click on "copy token". On the command line, run the
following (paste the clipboard for TOKEN):

{% highlight shell %}
$ vault token lookup TOKEN
{% endhighlight %}

You should be able to see the `policies` field set to `[default]` and
the `identity_policies` set to `[mypolicy]`.

To login using the command line, run the following:

{% highlight shell %}
$ vault login -method=oidc -path=MYPATH
{% endhighlight %}

You can omit the `-path=MYPATH` argument if you mounted the OIDC auth
method on the default path. A browser window should pop up, and the
rest is similar to logging in with the UI method explained above.
