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

As more employees, privileged identities, endpoints, applications, servers, and infrastructure are introduced, inconsistent naming would quickly make the environment harder to understand.

A good naming convention gives us context before we even open an object.

Is this a normal employee account?

Is it a privileged identity?

Is this a standard laptop, a privileged workstation, or a server?

Wire Finance therefore defines its naming standards before implementation begins.

### User Identity Naming

Wire Finance uses different naming patterns depending on the purpose of the identity.

| Identity | Naming Convention |
|---|---|
| **Standard User** | `firstname.lastname@domain` |
| **Privileged Administrator** | `adm-firstname.lastname@domain` |
| **Emergency Access** | `emergency-access-01@domain` |

A standard employee identity follows a simple and recognisable format:

`firstname.lastname@domain`

For example:

`olivia.carter@wirefinance.com`

A privileged administrator identity is deliberately distinguished from normal productivity access by using the `adm-` prefix:

`adm-jordan.lee@wirefinance.com`

Emergency access identities use a separate convention:

`emergency-access-01@wirefinance.com`

In each case, the name communicates something about the identity's purpose before we inspect its permissions.

> The examples in this design use `wirefinance.com` as the intended production-style domain. During the current lab implementation, users may initially use the Microsoft-provided `onmicrosoft.com` tenant domain until a custom domain is introduced.

### Employee Identifier Standard

A sign-in name is not the same thing as a permanent employee identifier.

Wire Finance assigns each workforce identity a unique employee ID using the format:

`WF-EMP-###`

For example:

| Employee | Employee ID |
|---|---|
| Jordan Lee | `WF-EMP-001` |
| Alex Morgan | `WF-EMP-002` |
| Taylor Reed | `WF-EMP-003` |
| Casey Ng | `WF-EMP-004` |
| Maya Patel | `WF-EMP-005` |
| Olivia Carter | `WF-EMP-006` |
| Ethan Brooks | `WF-EMP-007` |
| Noah Williams | `WF-EMP-008` |

Employee identifiers are:

- unique
- sequential
- permanent
- never reused after an employee leaves

The identifier also avoids embedding information such as department, location, job title, or employee name.

That matters because those things can change.

Olivia might move to another role or department later, but:

`WF-EMP-006`

should remain associated with her workforce record.

This creates a useful distinction:

`olivia.carter@wirefinance.com`  
= sign-in identity

`Olivia Carter`  
= human-readable identity

`WF-EMP-006`  
= permanent workforce identifier

### Device and Infrastructure Naming

The same naming discipline extends beyond user identities.

Wire Finance uses the `WF-` prefix for company-managed systems.

Examples include:

- `WF-LT-001` — standard corporate laptop
- `WF-PAW-001` — privileged access workstation
- `WF-TST-001` — deployment or test device
- `WF-DC-001` — domain controller
- `WF-SRV-001` — member server
- `WF-SYNC-001` — hybrid identity server
- `WF-JUMP-001` — management jump server
- `WF-LEGACY-001` — isolated legacy system

The purpose here is not simply to make names look tidy.

A recognisable name becomes useful when the same device later appears in management portals, security alerts, logs, investigations, and administrative tools.

The detailed rules for endpoint naming, numbering, and lifecycle are covered later in the **Device Provisioning Model**.

### Naming Principles

Across Wire Finance, naming should remain predictable.

Names should:

- communicate the object's purpose where appropriate
- follow a consistent organisational pattern
- avoid unnecessary personal or departmental information in device names
- remain understandable as the environment grows
- avoid reuse where the identifier is intended to be permanent

A name does not grant trust or permission.

For example, calling something `WF-PAW-001` does not make it a trusted privileged workstation by itself. Security still depends on the device's identity, configuration, management state, compliance, and access controls.

### Why Define This Before Implementation?

The important point is not simply that the names look organised.

The convention establishes consistency before objects begin multiplying across the environment.

That same naming logic can follow Wire Finance as it grows from an initial cloud environment into endpoint management, security operations, Active Directory, and hybrid identity.

Instead of creating objects first and trying to organise them later, we are deciding how the environment should identify itself before we build it.

We now know **what types of identities exist** and **how Wire Finance identifies them consistently**.

The next challenge is organising those identities so that access can be managed at scale rather than person by person.

**How should the structure of the business translate into groups and access assignments?**





