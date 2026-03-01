---
layout: post
title: How to install an old version of PHP using geerlingguy.php on Ubuntu target?
date: 2018-11-23
categories: [automation]
tags: [ansible, php]
---

Your playbook should look like so:

{% highlight yaml %}
---
- hosts: XYZ
  become: yes

  pre_tasks:
    - name: Add repository for old PHP
      apt_repository:
        repo: ppa:ondrej/php
        update_cache: yes
        state: present

  roles:
    - role: geerlingguy.apache
      ...

    - role: geerlingguy.php
      php_webserver_daemon: apache2
      php_default_version_debian: "7.1"
      php_version: "7.1"
      php_packages:
        - php7.1
        - php7.1-cli
        - etc...
{% endhighlight %}
