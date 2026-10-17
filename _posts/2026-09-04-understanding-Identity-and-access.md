---
layout: post
title: "Identity Before Detection: Designing IAM for Wire Finance"
author: Cybernetswork
categories: [IAM]
image: assets/images/active-directory/ad.png
description: "Follow the Wire Finance build as we design identity, access, device trust, Microsoft Entra ID, Intune, and eventually the security operations environment that depends on them."
tags: [iam]
---

### Introduction

Have you ever wondered how everything seems to work so smoothly when you join a new organisation?

You arrive on your first day, you're handed a company device, given your login details, and somehow the organisation already seems to know who you are, where you belong, and what you should have access to. It can feel a little like college: your name is already on the register before you've even figured out how the system behind it works.

But behind that seemingly simple experience is a much bigger story involving identity, access, roles, permissions, devices, authentication, and security.

Before we can meaningfully detect suspicious behaviour or respond to incidents, we first need to understand what normal access should look like. Who is this person? What should they be allowed to do? Which device are they using, and should that device be trusted? Identity gives the rest of the security environment context.

In this series, we'll pull back the curtain and explore how it all comes together as we build the security foundation for Wire Finance — our fictional organisation — beginning with identity and access.   

### Follow the Wire Finance Journey

Follow the chapters in order if you're building along with Wire Finance, or jump directly to the topic you want to explore.  

{% assign lessons = site.iam | where: "series", "wire-finance" | where: "show_on_master", true | sort: "chapter" %}

{% for lesson in lessons %}

### [{{ lesson.title }}]({{ lesson.url | relative_url }})

{{ lesson.description }}

{% endfor %}