---
layout: post
title: How to prepare for the CKA/CKAD/CKS exams
date: 2026-01-27
categories: [kubernetes]
tags: []
---

This is mostly a note for my future self.

These exams are performed on a remote desktop which has a browser and
a terminal. On the left, there is a side panel with the tasks to
perform. Each task contains links at the top to various documentations
related to the task itself. Just click on the link and it will open a
tab in the browser in the virtual desktop. You don't need to remember
all the URLs for each docs.

Those links aren't just to `kubernetes.io/docs` and the like, but to
specific pages in those websites that are relevant to the tasks. You
can get some hints on how to solve a task just by checking what
documentation is linked. For example, if you need to investigate
syscalls performed by suspicious containers and there is a link to
some falco documentation, you probably need to use falco to solve this
task.

In terms of preparing for the exams, it's just a fine to not pay for
an online course, and instead to study on your own. The CKA/CKAD/CKS
come with simulators you can use for 36 hours. Just use them, identify
your weaknesses and work on them. The simulators score your answers
and also provide solutions to the tasks. I think that's good enough in
term of preparation.

Please note the CKS is harder than the CKA and CKAD, mostly because it
involves more tools, such as:
  - bom
  - falco
  - Istio
  - Cilium
  - AppArmor
  - Seccomp
  - trivy
  - kube-bench
  - General Linux system administration

It seems that there are a few constants in all the CKA/CKAD/CKS exams
and simulators:
  - Kubernetes clusters are built using kubeadm
  - The underlying OS use apt as the package maanger
  - OS services are managed using systemd

That's it for now, good luck!
