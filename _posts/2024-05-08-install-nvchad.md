---
layout: post
title: How to install NvChad on Linux?
date: 2024-05-08
categories: [development]
tags: []
---

NvChad is a preset configuration for Neovim which provides you with a
pretty awesome IDE.

The first step is to install one of the Nerd Fonts for your terminal.
This is because they support a large number of icons. Go to the [Nerd
Fonts website](https://www.nerdfonts.com/font-downloads) and download
your favourite font, for example "JetBrainsMono Nerd Font". Then
install it like thus:

{% highlight shell %}
$ mkdir -p ~/.fonts
$ unzip JetBrainsMono.zip -d ~/.fonts
$ fc-cache -fv
{% endhighlight %}

You can check the font has been installed properly like so:

{% highlight shell %}
$ fc-list | grep JetBrains
$ rm JetBrainsMono.zip
{% endhighlight %}

Now modify the configuration of your terminal emulator to use the font
you just downloaded. By the way, the terminal should support
truecolor, which you can check like this:

{% highlight shell %}
$ echo $COLORTERM
truecolor
{% endhighlight %}

The next step is to install Neovim. The best way to run Neovim is with
the AppImage. Download latest neovim appimage from [the official
GitHub repo](https://github.com/neovim/neovim/releases). Make the
AppImage file executable and rename it to a useful name, such as
`nvim`. You probably want to move it to somewhere in your `PATH`.

Now it's time to install NvChad and its dependencies. Follow along:

{% highlight shell %}
$ sudo apt install git luarocks ripgrep xclip
$ [ -e ~/.config/nvim ] && mv ~/.config/nvim ~/.config/nvim.backup
$ [ -e ~/.local/share/nvim ] && mv ~/.local/share/nvim ~/.local/share/nvim.backup
$ rm -rf ~/.local/state/nvim
$ rm -rf ~/.cache/nvim
$ git clone https://github.com/NvChad/starter ~/.config/nvim --depth 1 && nvim
{% endhighlight %}

Wait for the packages to finish downloading, then run in Neovim:

{% highlight vim %}
:MasonInstallAll
{% endhighlight %}

And exit Neovim. Finally:

{% highlight shell %}
$ rm -rf ~/.config/nvim/.git
{% endhighlight %}

Now, you can even put your Neovim configuration in git if you want:

{% highlight shell %}
$ cd ~/.config/nvim
$ git init .
{% endhighlight %}

That's it, your NvChad is ready to use! Just run `nvim` (or however
you renamed the Neovim AppImage). A good starting point is to check
the NvChad cheatsheet: start `nvim` and press 'space', then 'c', then
'h'. Press `:q` to exit the cheatsheet.

To get started, you can change the theme to whatever you like. Start
`nvim` and press 'space', then 't', then 'h'.
