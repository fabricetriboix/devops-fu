---
layout: post
title: What is the best git workflow?
date: 2019-06-02
categories: [development]
tags: [git]
---

Many people have different opinions on this subject.

The best according to me is to have a `master` branch which contain
only deployable commits. Every commit on the `master` branch could
potentially be deployed.

Every time you need to add a feature or fix a bug, create a branch,
work on it and when you are ready to merge it back to `master`, do the
following:

 1. Test your changes
 2. Merge `master` on your feature branch
 3. Test your changes again
 4. Merge your branch into `master` without fast-forward (this will
    create a commit even if there had been no changes in the `master`
    branch)

So that would look something like this:

{% highlight shell %}
$ ./run-some-tests
$ git checkout MYBRANCH
$ # ...Do your changes...
$ git checkout master
$ git pull
$ git checkout MYBRANCH
$ git merge master
$ ./run-some-tests
$ git checkout master
$ git merge --no-ff MYBRANCH
$ git log --first-parent master  # show only commits done on the master branch
{% endhighlight %}

That would allow the master branch to have a clean history.
