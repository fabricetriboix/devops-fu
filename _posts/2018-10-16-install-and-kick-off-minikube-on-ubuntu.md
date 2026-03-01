---
layout: post
title: How to install and kick off minikube on Ubuntu?
date: 2018-10-16
categories: [kubernetes]
tags: [docker, ubuntu]
---

Install kubectl:

{% highlight shell %}
$ sudo apt update
$ sudo apt install apt-transport-https
$ curl -sSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
$ echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee -a /etc/apt/sources.list.d/kubernetes.list
$ sudo apt update
$ sudo apt install kubectl
{% endhighlight %}

Install VirtualBox:

{% highlight shell %}
$ sudo apt update
$ sudo apt install virtualbox virtualbox-ext-pack
{% endhighlight %}

Install minikube (replace v1.17.1 with the latest version available
for you):

{% highlight shell %}
$ wget https://github.com/kubernetes/minikube/releases/download/v1.17.1/minikube-linux-amd64
$ mv minikube-linux-amd64 minikube
$ chmod +x minikube
$ sudo cp minikube /usr/local/bin/
$ rm minikube
{% endhighlight %}

Create the VM and start the cluster (this will create/update the file
`~/.kube/config`):

{% highlight shell %}
$ minikube config set driver virtualbox  # Set virtualbox as the default driver
$ minikube start  # use `minikube start --help` for more options
$ kubectl cluster-info
{% endhighlight %}

To get the IP address of the cluster:

{% highlight shell %}
$ minikube ip
$ sudo -e /etc/hosts  # if you want to assign names to this IP address
{% endhighlight %}

To show the Minikube dashboard:

{% highlight shell %}
$ minikube dashboard
{% endhighlight %}

To stop the cluster (shuts down the VM and preserve all states):

{% highlight shell %}
$ minikube stop
{% endhighlight %}

To restart the VM and cluster:

{% highlight shell %}
$ minikube start
{% endhighlight %}

To delete the cluster (and the VM):

{% highlight shell %}
$ minikube delete
{% endhighlight %}
