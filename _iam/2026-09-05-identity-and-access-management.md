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


{% assign sections = site.iam | where: "series", "wire-finance" | where: "parent", "iam-design" | sort: "order" %}

{% for section in sections %}

#### [{{ section.title }}]({{ section.url | relative_url }})

{% endfor %}