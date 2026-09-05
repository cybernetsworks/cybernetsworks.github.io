---
layout: post
title: "Identity Before Detection: Designing IAM for Wire Finance"
author: Cybernetswork
categories: [IAM]
image: assets/images/active-directory/ad.png
tags: [iam]
---

**Introduction**

Have you ever wondered how everything seems to work so smoothly when you join a new organisation?

You arrive on your first day, you're handed a company device, given your login details, and somehow the organisation already seems to know who you are, where you belong, and what you should have access to. It can feel a little like those college days when your name appeared on the school register before you even fully understood how the system behind it worked.

But behind that seemingly simple experience is a much bigger story involving identity, access, roles, permissions, devices, authentication, and security.

In this series, we'll pull back the curtain and explore how it all comes together as we build the identity foundation for Wire Finance — our fictional organisation — from the ground up.      

**Follow the Wire Finance Journey**

{% assign lessons = site.iam | |where: "series", "wire-finance" | sort: "chapter" %}

{% for lesson in lessons %}
# [{{ lesson.title }}]({{ lesson.url | relative_url }})

{{ lesson.description }}

{% endfor %}
