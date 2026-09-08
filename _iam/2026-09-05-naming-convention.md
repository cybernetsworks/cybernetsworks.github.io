---
layout: lesson
title: "Naming Convention"
series: wire-finance
parent: iam-design
order: 2
description: A name should tell us more than who or what something is. Wire Finance uses consistent naming to make identities, devices, and infrastructure easier to recognise as the environment grows.
---


At the beginning of a small environment, naming can feel like an afterthought.

There may only be a few users, a handful of devices, and perhaps one or two servers. It can be tempting to name things as they are created and move on.

But Wire Finance is being designed to grow.

As more users, administrative accounts, endpoints, servers, and identity infrastructure are introduced, inconsistent naming would quickly make the environment harder to understand.

A good naming convention gives us context before we even open the object.

Is this a normal employee account?

Is it a privileged identity?

Is this device a standard laptop, a domain controller, or a privileged workstation?

Wire Finance therefore defines its naming standards before implementation begins.

#### User Identity Naming

Wire Finance uses different naming patterns depending on the purpose of the identity.

| Identity | Naming Convention |
|---|---|
| **Standard User** | `firstname.lastname@domain` |
| **Privileged User** | `adm-firstname.lastname@domain` |
| **Emergency Access** | `emergency-access-01@domain` |

A standard employee identity follows a simple and recognisable format:

`firstname.lastname@domain`

For example:

`john.doe@wirefinance.com`

Administrative identities are deliberately distinguished from normal employee identities by using the `adm-` prefix:

`adm-firstname.lastname@domain`

This makes it immediately clear that the identity exists for privileged activity rather than everyday productivity work.

Emergency access identities follow their own convention:

`emergency-access-01@domain`

Again, the name communicates the account's purpose without requiring us to investigate further.

#### Device and Infrastructure Naming

The same principle applies to devices and infrastructure.

Wire Finance uses the `WF-` prefix to identify company-managed systems, followed by a short identifier describing the system's purpose.

| System Type | Naming Convention / Example |
|---|---|
| **Standard Laptop** | `WF-LT-001` |
| **Privileged Access Workstation** | `WF-PAW-001` |
| **Deployment Test Device** | `WF-TST-001` |
| **Domain Controller** | `WF-DC-001` |
| **Secondary Domain Controller** | `WF-DC-002` |
| **Member Server** | `WF-SRV-001` |
| **Hybrid Identity Server** | `WF-SYNC-001` |
| **Management Jump Server** | `WF-JUMP-001` |
| **Isolated Legacy System** | `WF-LEGACY-001` |

The names begin to tell their own story.

`WF-LT-001` is a Wire Finance laptop.

`WF-PAW-001` is a privileged access workstation.

`WF-DC-001` is a domain controller.

`WF-SYNC-001` is intended for hybrid identity.

This becomes increasingly useful as the environment expands and the same systems begin appearing in management portals, security alerts, logs, investigations, and administrative tools.

---

#### Why Define This Before Implementation?

The important point is not simply that the names look tidy.

The convention establishes consistency across the environment before we begin creating resources.

That means the same naming logic can follow Wire Finance as it moves from its initial cloud environment into future Active Directory and hybrid identity infrastructure.

Instead of creating objects first and trying to organise them later, we are deciding how the environment should identify itself before we build it.

And naming is only one part of that identity structure.

Some of the accounts we have defined will carry significantly more power than others.

That brings us to the next question:

**How should Wire Finance control privileged and emergency access?**