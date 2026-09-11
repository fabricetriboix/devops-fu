---
layout: post
title: How to sync a forked repo on github?
date: 2026-09-11
categories: [git]
tags: []
---

Assumptions:
  - You have two remotes configured in your repo
  - Your remote is named `origin`
  - The remote of the forked repo is named `upstream`
  - Main branches on both your and the forked repo are named `main`

Run the following commands:

```sh
$ git fetch upstream --tags --force --prune
$ git checkout main
$ git rebase upstream/main
$ git push --force-with-lease origin main
$ git push origin --tags
```
