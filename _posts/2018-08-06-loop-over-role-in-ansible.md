---
layout: post
title: How to loop over a role in Ansible?
date: 2018-08-06
categories: [automation]
tags: [ansible]
---

Don’t use `roles:` but `tasks:` and use the
[include_role](https://docs.ansible.com/ansible/latest/modules/include_role_module.html)
module. For example:

{% highlight yaml %}
---
# roles/myrole/tasks/main.yml
- debug: var=bla

---
# playbook
- hosts: localhost
  connection: local
  gather_facts: no
  tasks:
   - name: Loop over role
     include_role:
      name: myrole
     vars:
      bla: "{{ item }}"
     with_items:
      - hello world
      - hi
{% endhighlight %}
