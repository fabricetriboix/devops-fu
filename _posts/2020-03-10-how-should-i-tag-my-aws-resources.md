---
layout: post
title: How should I tag my AWS resources?
date: 2020-03-10
categories: [aws]
tags: []
---

The primary purposes of tags are:

 - To provide human-readable information about the resources
 - As part of automation
 - Filter resources

Here are some use cases related to providing human-readable
information:

 - Allow a human to semantically understand what a resource is; eg:
   Name, Version
 - Identify what environment a resource belongs to (staging, prod,
   etc.); eg: Environment
 - Identify who is responsible for a given resource; eg: Owner,
   Project, Business Unit, Department
 - Identify which project or product a given resource belongs to; eg:
   Project
 - Identify whether a resource is managed automatically
   (CloudFormation, Terraform) or not (human); eg: CreatedBy,
   ManagedBy

Here are some use cases related to automation:

 - Turn on/off monitoring or some debugging stuff based on tag values;
   eg: Environment, Version, Project, OptIn, OptOut
 - Start/stop or scale out/in resources based on tag values; eg:
   DateTime, Customer
 - Apply some automation based on tag values; eg: Environment,
   DateTime, OptIn, OptOut, Version, Customer, Project
 - Date of an EBS snapshot; eg: DateTime
 - This list could be endless...
 - Run some commands on certain EC2 instances; eg: OS, Environment, Project

Here are some use cases related to filtering resources:

 - Allocate costs to categories, business units, etc.; eg: Owner,
   Business Unit, Department, Cost Center, Cost Category, Customer
 - See also
   [this](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/aws-tags.html) for AWS-generated tags that help with cost allocation
 - Build inventories of resources; this is especially useful for
   [resource
   groups](https://docs.aws.amazon.com/ARG/latest/userguide/welcome.html);
   eg: Project, Environment, Customer, Department

Finally, some other use cases:

 - [Apply IAM permissions based on tag
   values](https://aws.amazon.com/premiumsupport/knowledge-center/iam-policy-tags-restrict/)
 - Identify resources where a certain compliance is required
 - Governance

Some tips on how to use tags effectively:

 - Settle on a naming and case conventions
 - Apply generously; better to have too many tags than not enough
 - Tags can be changed easily, so just do it and adapt as you go along
   or when requirements change

See this AWS article for more information: [AWS Tagging
Strategies](https://aws.amazon.com/answers/account-management/aws-tagging-strategies/).
