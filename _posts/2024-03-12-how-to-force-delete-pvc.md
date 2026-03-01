---
layout: post
title: How to force delete a PVC?
date: 2024-03-12
categories: [kubernetes]
tags: []
---

Sometimes you want to delete a PVC and it just doesn't want to die.
Usually that's because a StatefulSet has not been deleted properly and
the volumes are still dangling. If the PVC and the PV are still
bound together, you won't be able to delete either of them. The issue
as usual in this situation is the `finalizers`, which must be removed
manually to get the PVC to fully terminate.

Here is how to do it. First delete the PVC as usual:

{% highlight shell %}
$ kubectl delete pvc/$MYPVC
{% endhighlight %}

The `kubectl` command will say that the PVC has been deleted then it
will hang. Press ctrl-C to terminate the command. If you list the
PVCs, you will see that its status is `Terminating`.

Now you need to set the `finalizers` to `null`. You can `kubectl edit`
the PVC, or patch it like so:

{% highlight shell %}
{% raw %}
$ kubectl patch pvc/$MYPVC -p '{"metadata": {"finalizers": null}}'
{% endraw %}
{% endhighlight %}

Finally, do something similar to the PV if you want to get rid of it
too.
