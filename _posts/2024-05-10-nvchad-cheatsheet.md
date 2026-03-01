---
layout: post
title: NvChad cheatsheet
date: 2024-04-10
categories: [development]
tags: []
---

Install syntax highlighting for Terraform: `:Lazy load
nvim-treesitter`, then `TSUpdate terraform`. See List of supported
languages
[here](https://github.com/nvim-treesitter/nvim-treesitter?tab=readme-ov-file#supported-languages).

Install the `farmergreg/vim-lastplace` plugin to restore the cursor to
the last known position when reopening a file.

Install language servers, formatters, etc. using Mason: `:Mason`.
Hover over which package you are interested in and press 'i' to
install it.

I like to be able to use the up/down keys for the pop up menu, and `tab` to
confirm the selection. This can be done by modifying the
`~/.config/nvim/lua/plugins/init.lua` file and adding the following entry to
the table:

{% highlight lua %}
  {
    "hrsh7th/nvim-cmp",
    opts = function()
      local cmp = require("cmp")
      local conf = require("nvchad.configs.cmp")

      local mymappings = {
        ["<Up>"] = cmp.mapping.select_prev_item(),
        ["<Down>"] = cmp.mapping.select_next_item(),
        ["<Tab>"] = cmp.mapping.confirm {
          behavior = cmp.ConfirmBehavior.Replace,
          select = true,
        },
      }

      conf.mapping = vim.tbl_deep_extend("force", conf.mapping, mymappings)
      return conf
    end,
  },
{% endhighlight %}

The above essentially extends the [default nvim-cmp NvChad configuration](https://github.com/NvChad/NvChad/blob/v2.5/lua/nvchad/configs/cmp.lua).

Interesing: https://github.com/PatrickKoss/NvChad-Custom-Config
