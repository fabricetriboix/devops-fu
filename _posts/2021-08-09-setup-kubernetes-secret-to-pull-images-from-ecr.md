---
layout: post
title: How to setup a Kubernetes secret to pull images from ECR?
date: 2021-08-09
categories: [kubernetes]
tags: []
---

Example for AWS ECR:

{% highlight shell %}
$ aws ecr get-login-password | docker login -u AWS --password-stdin DOCKER_REGISTRY_DOMAIN_NAME
$ kubectl create secret generic SECRET_NAME --from-file=.dockerconfigjson=$HOME/.docker/config.json --type=kubernetes.io/dockerconfigjson
{% endhighlight %}

Now you can use `SECRET_NAME` as the name of the secret to pull images.
Usually Helm charts will have a `pullImageSecrets` variable for that.
