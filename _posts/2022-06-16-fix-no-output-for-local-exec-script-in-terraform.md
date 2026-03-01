---
layout: post
title: How to fix no output on local-exec script in Terraform?
date: 2022-06-16
categories: [terraform]
tags: []
---

When you use variables in a local-exec script, the entire output of
the script will be removed if just one of those variable is considered
"sensitive" by Terraform. The output will look like this:

> null_resource.xyz (local-exec): (output suppressed due to sensitive value in config)

The solution is to use the `nonsensitive()` function, but you should
really do that only for debugging purposes.

More information
[here](https://github.com/hashicorp/terraform/issues/27154).
