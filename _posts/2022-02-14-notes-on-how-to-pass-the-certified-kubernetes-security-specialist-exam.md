---
layout: post
title: Notes on how to pass the Certified Kubernetes Security Specialist exam
date: 2022-02-14
categories: [kubernetes]
tags: []
---

The CKS is definitely the hardest of the three Kubernetes
certifications from the LinuxFoundation (the other two being the CKA
and the CKAD). The time runs much faster because the questions are
harder and the scope is broader. I finished both the CKA and CKAD
before the time, but I couldn’t finish the CKS (I still passed the
exam, but that was a bit more borderline). Here are some notes to help
pass it:

 - For some questions, you can spend some time verifying that you got
   it right. For example, if setting up a network policy, you can
   create some pods and check that the communication between the pods
   is as expected. That will be a lot of time spent on those
   verifications rather then answering other questions. My advice is
   to learn network policies inside out and don't verify your answers.
   Ditto for CIS benchmarks: Just perform the task and move on, don't
   verify it. You can mark those questions and come back to verify
   them at the end of the exam if you have time.
 - Before the exam, spend some time playing with Falco and getting an
   understanding of all the variables you can play with for conditions
   and outputs.
 - Before the exam, do a lab on your own setting up an
   `ImagePolicyWebhook` with a trivy scanner. You can use [this
   project](https://github.com/linuxacademy/content-cks-trivy-k8s-webhook)
   for the scanner, which has an image available
   [here](https://hub.docker.com/r/linuxacademycontent/trivy-k8s-webhook).
 - For a number of topics, such as PodSecurityPolicies and AppArmor,
   some explanations or even manifest snippets can be found in the
   official Kubernetes documentation. Learn how to find back the
   relevant pages or bookmark them for the exam.

