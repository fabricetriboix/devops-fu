---
layout: post
title: How to store the Terraform state in GitLab?
date: 2024-03-25
categories: [terraform]
tags: [gitlab]
---

GitLab natively supports storing and locking Terraform states. So if
your project uses both GitLab and Terraform, you might consider this
option to save your Terraform states.

First of all, your Terraform configuration should contain the
following (typically in a file named `providers.tf`):

{% highlight tf %}
terraform {
  backend "http" {}
}
{% endhighlight %}

Next, you will need to create a [GitLab personal access
token](https://gitlab.com/-/user_settings/personal_access_tokens) with
all the scopes included. Save the token because GitLab will only show
it to you once.

Now you can run the following steps:

{% highlight shell %}
$ export PROJECT_ID=$YOUR_GITLAB_PROJECT_ID
$ export TF_USERNAME=$YOUR_GITLAB_USERNAME
$ export TF_PASSWORD=$THE_GITLAB_PERSONAL_ACCESS_TOKEN
$ export TF_STATE=$YOUR_CHOSEN_TERRAFORM_STATE_NAME
$ export TF_ADDRESS="https://gitlab.com/api/v4/projects/${PROJECT_ID}/terraform/state/${TF_STATE}"
$ terraform init \
  -backend-config=address=${TF_ADDRESS} \
  -backend-config=lock_address=${TF_ADDRESS}/lock \
  -backend-config=unlock_address=${TF_ADDRESS}/lock \
  -backend-config=username=${TF_USERNAME} \
  -backend-config=password=${TF_PASSWORD} \
  -backend-config=lock_method=POST \
  -backend-config=unlock_method=DELETE \
  -backend-config=retry_wait_min=5
{% endhighlight %}

Now running `terraform plan`, `terraform apply` and `terraform state`
will be done against the Terraform plan saved in GitLab.
