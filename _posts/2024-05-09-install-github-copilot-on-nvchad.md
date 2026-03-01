---
layout: post
title: How to install GitHub Copilot on NvChad?
date: 2024-05-09
categories: [development]
tags: []
---

Edit the `~/.config/nvim/lua/plugins/init.lua` file and add the
following in the array:

{% highlight lua %}
  {
    "github/copilot.vim",
    lazy = false
  },
{% endhighlight %}

Then start nvim and enter `:Lazy` to bring up the lazy.nvim dashboard,
and press 'I' to install the missing package(s). Press 'q' to exit the
lazy dashboard. Now enter `:Copilot setup` to set Copilot up.

By the way, GitHub Copilot requires a recent version of NodeJS. The
easiest way to install a recent version of NodeJS is to use `nvm`:

{% highlight shell %}
$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
$ nvm list-remote
$ nvm install v22.1.0
{% endhighlight %}
