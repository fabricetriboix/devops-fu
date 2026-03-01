---
layout: post
title: How to manually remove finalizers from a namespace?
date: 2022-12-02
categories: [kubernetes]
tags: []
---

It is quite a common occurence for a namespace to be stuck in a
"Terminating" state. This is because the namespace has finalizers
attached to it that do not want to finalize, usually for a good
reason. But if you are just experimenting, you might just want this
namespace to disappear. Here are some manual steps to do that (NB: do
it at your own risks, no warranty it will work for you, disclaimer,
yadi yada):

In one terminal, run `kubectl proxy`. In another terminal, run the
following:

{% highlight shell %}
{% raw %}
$ kubectl get namespaces  # Note the namespace you want to terminate, say MYNS in this example
$ kubectl get namespace/MYNS -o json > myns.json  # Save the namespace manifest in JSON
{% endraw %}
{% endhighlight %}

Edit the JSON file. You should see something like that:

{% highlight json %}
...
    "spec": {
        "finalizers": [
            "kubernetes"
        ]
    },
...
{% endhighlight %}

Remove everything in the "finalizers" list. Then run this command:

{% highlight shell %}
$ curl -k -H "Content-Type: application/json" -X PUT --data-binary @tmp.json http://127.0.0.1:8001/api/v1/namespaces/MYNS/finalize
{% endhighlight %}

The namespace should be terminated now.
