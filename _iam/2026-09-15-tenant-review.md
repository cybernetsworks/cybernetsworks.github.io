---
layout: lesson
title: "Tenant Review"
series: wire-finance
parent: entra-id-implementation
order: 2
description: "Before making changes, we establish the tenant's starting state. Seven simple checks give Wire Finance a clean baseline for everything we build next."
---

The Wire Finance Microsoft environment now exists.

We have a tenant.

We have Microsoft 365 E5 licensing.

And the identity capabilities we need are available.

But before creating users, groups, roles, or policies, there is one more thing we should do:

**find out exactly what is already there.**

This gives us a known starting point before we begin changing the environment.

#### Why Review the Tenant First?

A newly prepared tenant is not necessarily an empty tenant.

Accounts may already exist.

Administrative roles may already be assigned.

Security Defaults may already be enabled.

Groups or devices may have appeared during setup.

If we start building without checking the existing state, it becomes harder to tell which settings came from Microsoft, which were created during setup, and which were deliberately implemented for Wire Finance.

So before making further changes, I recorded a small tenant baseline.

The review focuses on just seven things.

#### The Seven-Point Tenant Review

##### 1. Tenant / Display Name

First, confirm the organisation displayed in the Microsoft Entra environment.

```text
Tenant / Display Name:
[Record the tenant name here]
```

This sounds basic, but it confirms that we are working inside the correct environment before making any configuration changes.

##### 2. Primary `onmicrosoft.com` Domain

Every Microsoft Entra tenant receives an initial Microsoft domain.

Record the primary:

```text
<tenant>.onmicrosoft.com
```

For the project, this gives us another way to identify the tenant and provides the default domain that existed before any future custom-domain work.

```text
Primary onmicrosoft.com domain:
[Record the domain here]
```

##### 3. Available Licensing

Next, confirm the licences available in the tenant.

For Wire Finance, the environment currently uses:

```text
Microsoft 365 E5
25 seats
```

Microsoft Entra ID P2 capabilities have also been confirmed as available in the environment.

At this stage, we are not configuring every feature included with the licence.

We are simply confirming that the environment can support the identity and security controls we intend to build later.

##### 4. Security Defaults

Next, check the current state of **Security Defaults**.

Record whether it is:

```text
Enabled
```

or:

```text
Disabled
```

The important thing during this review is not to immediately change the setting.

We first want to know the tenant's existing state.

Later, when we begin implementing authentication and Conditional Access, we can make deliberate decisions about how those controls should operate.

```text
Security Defaults:
[Enabled / Disabled]
```

##### 5. Global Administrators

Next, identify how many identities currently hold the **Global Administrator** role.

```text
Global Administrators:
[Record number]
```

This matters because Global Administrator is one of the most powerful roles in the tenant.

Our IAM design already established that broad administrative access should not be used for routine administration.

For now, however, we are only recording the existing state.

The privileged-access model will be implemented later.

##### 6. Existing Users and Groups

Before creating the Wire Finance identity structure, record how many users and groups already exist.

```text
Existing users:
[Record number]

Existing groups:
[Record number]
```

This gives us a clean baseline.

If the tenant currently contains only setup or administrative identities, we now know that every workforce identity and access group created from this point forward belongs to the Wire Finance implementation.

That will make later validation much easier.

##### 7. Existing Devices

Finally, check whether any devices are currently registered or joined to the tenant.

```text
Existing devices:
[Record number]
```

Device identity is not being implemented yet.

Intune enrolment, device groups, compliance, and endpoint security will come later.

For now, we simply want to know whether the tenant already contains any device objects before that phase begins.

## Recording the Starting State

The completed review can be summarised in a simple table.

| Check | Starting State |
|---|---|
| **Tenant / Display Name** | `[Record value]` |
| **Primary `onmicrosoft.com` domain** | `[Record value]` |
| **Microsoft 365 licence** | Microsoft 365 E5 |
| **Licensed seats** | 25 |
| **Microsoft Entra ID** | P2 available |
| **Security Defaults** | `[Enabled / Disabled]` |
| **Global Administrators** | `[Record number]` |
| **Existing users** | `[Record number]` |
| **Existing groups** | `[Record number]` |
| **Existing devices** | `[Record number]` |

This becomes our **before picture**.

From this point onward, when users, groups, roles, policies, and devices begin appearing in the tenant, we know they are part of the environment we are deliberately building.

#### Establishing the Baseline Before the Build

The Tenant Review did not create anything.

That was the point.

```text
Environment Setup
        ↓
Tenant Exists
        ↓
Tenant Review
        ↓
Starting State Known
        ↓
Implementation Begins
```

We now know what environment we are working with and what existed before the Wire Finance identity architecture was introduced.

The next step is where the tenant begins to change.

Earlier, we designed company, department, role, exception, pilot, and privileged-access groups.

Now we can start turning those designs into actual Microsoft Entra security groups.

**What happens when our access model stops being a diagram and starts becoming real tenant objects?**