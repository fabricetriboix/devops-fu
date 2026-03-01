---
layout: post
title: How to avoid using AWS credentials in GitHub Actions?
date: 2024-03-25
categories: [aws]
tags: [github]
---

If you want your GitHub Actions pipeline to perform some actions in
AWS, the usual way is to create an IAM user with some keys, and then
to store those keys as secrets in GitHub. This is not ideal because
those long-lived secrets have to be copy/pasted and can potentially be
leaked. A more modern, better way is to configure your AWS account to
accept GitHub as an identity provider.

First, navigate to the IAM console on your browser, and click "Add
provider", and fill in the form like so:

 - "Provider type": "OpenID connect"
 - "Provider URL": `https://token.actions.githubusercontent.com`
 - "Audience": `sts.amazonaws.com`

You now need to create an IAM role for your GitHub pipeline. Create an
IAM policy allowing your pipeline to perform the required actions.
Ideally, follow the principle of least privilege. Then create an IAM
role. AWS makes it easy for you to configure that role to be used by
GitHub:

 - "Trusted entity type": "Web identity"
 - "Identity provider": Select the identity provider you created before
 - "Audience": `sts.amazonaws.com`
 - "GitHub organization": Type in the name of your GitHub organization
 - "GitHub repository": Optionally type in the name of the GitHub repo
   that will run that particular pipeline
 - "GitHub branch": You can restrict the role to specific branches

Attach the policy you previously created and give the role a good,
descriptive name. Note down the role ARN.

Finally, you can configure your GitHub pipeline to use this IAM role.
Here is an example of what such a pipeline would look like:

{% highlight yaml %}
name: Pipeline doing stuff in AWS

on:
  push

permissions:
  id-token: write   # This is required for requesting the JWT
  contents: read    # This is required for actions/checkout

jobs:
  myjob:
    name: My awesome job
    runs-on: ubuntu-latest
    steps:
    - name: Checkout
      uses: actions/checkout@v3

    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v4
      with:
        role-to-assume: arn:aws:iam::258302884997:role/RoleForGithubActionsCmsRepo
        role-session-name: github-cms-pipeline
        aws-region: eu-west-1

    - name: Do stuff in AWS
      run: aws sts get-caller-identity
{% endhighlight %}

Both GitHub and AWS make it very easy to use this method, so make sure
you stop copy/pasting those AWS keys moving forward!

**Update 27 Aug 2024**: I would strongly suggest you increase the
assume role duration to more than 1 hour. Otherwise, you might run the
risk that the temporary credentials expire before the pipeline is
finished. If this happens with Terraform and you are using S3 to store
the state file, Terraform won't be able to write the state file with
the latest changes and you might be in serious trouble. You will need
to update the maximum session duration for the IAM role itself, and
use the `role-duration-seconds` parameter of the
`aws-actions/configure-aws-credentials` module to more than 1 hour.
