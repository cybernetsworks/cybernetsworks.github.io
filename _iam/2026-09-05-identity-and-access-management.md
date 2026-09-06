---
layout: lesson
title: "Identity and Access Management"
chapter: 4
series: wire-finance
show_on_master: true
author: Cybernetswork
image: assets/images/active-directory/profile.png
description: Wire Finance has people, departments, remote workers, cloud services, and sensitive data. Now we need to decide who gets access to what — and why.
---


Wire Finance now has employees, departments, cloud services, remote workers, and sensitive information.

The next challenge is deciding who should have access to what — and ensuring that access remains appropriate as people join, move through the organisation, or take on more privileged responsibilities.

#### Design Purpose

The purpose of the Identity and Access Management design is to ensure that users, administrators, devices, applications, and workloads receive only the access required to perform their authorised functions.

But simply creating accounts and assigning permissions is not enough.
Wire Finance needs an identity model that can grow with the organisation while keeping access controlled, understandable, and auditable.

#### Design Principles
To achieve this, the IAM design will be guided by four core principles:

##### Role-Based Access Control
Access should be aligned with job responsibilities rather than assigned randomly to individual users.

##### Seperation of Duties
Sensitive or administrative activities should not depend on a single identity having unrestricted control.

##### Zero Trust
Access should not be trusted simply because a user or device is inside the organisation. Identity, device state, authentication, and context all contribute to access decisions.

These principles will shape every identity decision we make as Wire Finance grows.

##### Explore the IAM Design

The next sections break the design into the individual components that make the identity model work.


{% assign sections = site.iam | where: "series", "wire-finance" | where: "parent", "iam-design" | sort: "order" %}

{% for section in sections %}

##### [{{ section.title }}]({{ section.url | relative_url }})

{% endfor %}