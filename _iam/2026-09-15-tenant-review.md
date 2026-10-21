---
layout: lesson
title: "Tenant Review"
series: wire-finance
parent: entra-id-implementation
order: 2
description: "Before building the Wire Finance identity model, we review the Microsoft tenant, verify its capabilities, and establish a starting baseline."
---

The Wire Finance Microsoft environment now exists.

We have an organisational tenant, Microsoft 365 E5 licensing, and the identity capabilities needed to support our design.

But having a working tenant does not mean we understand everything already configured inside it.

Before creating workforce identities, groups, or administrative roles, we need to answer an important question:

**What already exists in this environment, and what are we about to change?**

That is the purpose of our tenant review.

### Why Review the Tenant First?

A newly established Microsoft tenant is not necessarily empty.

An initial administrator already exists.

Security settings may have default configurations.

Groups or other objects may have been created during setup.

And some settings may influence how our users authenticate or access resources.

If we begin building without understanding that starting state, we risk confusing existing configurations with the changes we deliberately introduce.

So before proceeding, we need a baseline.

The approach is simple:

**Inspect first. Record what exists. Make changes only when we understand why they are needed.**

For Wire Finance, the review focuses on seven areas.

### The Seven-Point Tenant Review

#### 1. Tenant Identity

First, confirm that we are working inside the correct Microsoft Entra tenant.

Open the [Microsoft Entra admin center](https://entra.microsoft.com/) and navigate to:

```text
Entra ID
   ↓
Overview
   ↓
Properties
```

Here, we can review the tenant's identifying information, including its tenant ID.

This matters because administrative changes affect the tenant we are currently connected to.

Before creating users or configuring security policies, we need to be certain that we are working in the intended organisational environment.

#### 2. Tenant Domain

Next, review the domains associated with the tenant.

```text
Entra ID
   ↓
Domain names
```

Microsoft provides an initial `onmicrosoft.com` domain when an organisational tenant is created.

Wire Finance currently uses this Microsoft-provided tenant domain for its lab identities.

Our IAM design uses production-style names such as:

```text
olivia.carter@wirefinance.com
```

However, `wirefinance.com` is our target naming convention, not a custom domain already configured in the lab.

For now, the implemented identities use the tenant's `onmicrosoft.com` suffix.

**The naming principle remains the same; only the domain suffix differs.**

Custom-domain integration can be addressed later without changing the identity model we have designed.

#### 3. Licensing and Identity Capabilities

Now we need to verify that the environment has the licensing required for our planned implementation.

In the Microsoft 365 admin center, the subscription can be reviewed through:

```text
Billing
   ↓
Your products
```

The verified Wire Finance environment has:

| Component | Verified State |
|---|---|
| Microsoft subscription | Microsoft 365 E5 |
| Licensed seats | 25 |
| Microsoft Entra ID | P2 capabilities operational |
| Initial identity model | Cloud-only |

This is an important checkpoint.

Our design includes advanced identity capabilities such as Conditional Access, Identity Protection, and Privileged Identity Management.

Microsoft Entra ID P2 makes these capabilities available.

However, **licensing availability and security configuration are two different things**.

We have confirmed the capabilities required for the implementation. The actual controls still need to be configured and validated.

#### 4. Security Defaults

Next, check whether Security Defaults are enabled.

Security Defaults provide Microsoft's baseline identity protections, including multifactor authentication requirements.

The setting can be reviewed through:

```text
Entra ID
   ↓
Overview
   ↓
Properties
   ↓
Manage security defaults
```

At this point, the objective is observation, not modification.

We need to know whether Security Defaults are already influencing authentication behaviour before introducing our own Conditional Access policies.

This is especially important because Wire Finance intends to implement more deliberate access controls later.

We should not disable or replace existing protection without understanding what will take its place.

#### 5. Global Administrator Assignments

Next, identify who currently holds the Global Administrator role.

```text
Entra ID
   ↓
Roles & admins
   ↓
Global Administrator
   ↓
Assignments
```

Global Administrator is a highly privileged Microsoft Entra directory role.

During initial setup, administrative permissions are needed to establish the environment.

But the presence of an initial administrator should not become an excuse for permanent, unrestricted administration.

Our IAM design already established a separation between standard identities and dedicated privileged identities.

For this review, we are interested in identifying the existing role assignments.

We are not yet implementing the final privileged-access model.

That belongs to a later stage of this chapter.

#### 6. Existing Users and Groups

Before adding our workforce, we also need to inspect the existing directory objects.

For users:

```text
Entra ID
   ↓
Users
   ↓
All users
```

For groups:

```text
Entra ID
   ↓
Groups
   ↓
All groups
```

Here, we look for identities and groups that already exist.

Not every object in a tenant was necessarily created as part of our planned IAM structure.

For example, the initial administrative account belongs to the tenant's setup history, not the standard Wire Finance workforce.

Likewise, an existing group should not automatically be treated as one of our designed security groups.

This distinction will become important when we begin implementing company-wide, department, role, pilot, and privileged-access groups.

**We need to understand the objects already present before assigning new business meaning to them.**

#### 7. Existing Devices

Finally, check the devices associated with the tenant.

```text
Entra ID
   ↓
Devices
   ↓
All devices
```

At this stage of the journey, our focus is still identity implementation.

But we already know that devices will eventually become an important part of how Wire Finance evaluates trust.

Before introducing Microsoft Intune and Windows Autopilot, it is useful to understand whether device identities already exist.

We are not configuring device management here.

We are establishing the starting point against which later enrolment and provisioning can be validated.

### What We Established

The tenant review helps separate the existing Microsoft environment from the configurations we deliberately introduce for Wire Finance.

The verified foundation is:

```text
Microsoft Organisational Tenant
             ↓
Microsoft-Provided Tenant Domain
             ↓
Microsoft 365 E5 — 25 Seats
             ↓
Microsoft Entra ID P2 Available
             ↓
Identity Implementation Foundation
```

The review also identifies the settings and objects that need to be accounted for before making changes:

- Security Defaults
- Existing administrative role assignments
- Existing users and groups
- Existing device identities

These are baseline observations, not instructions to change everything immediately.

In particular, counts or settings observed after implementation should not be mistaken for the tenant's original starting state.

The purpose is to make sure that future configuration changes can be understood and validated against a known environment.

### From Tenant Review to Workforce Identity Creation

The tenant review itself does not create any new identities, groups, or access policies.

It establishes the context for the changes that follow.

```text
Environment Setup
       ↓
Tenant Review
       ↓
Starting Environment Understood
       ↓
Workforce Identity Creation
       ↓
Access and Group Implementation
```

Now we can move from examining the environment to building the organisation inside it.

Earlier, we designed a 25-person workforce and established standards for identity types, naming conventions, employee identifiers, and access requirements.

For the initial implementation, we selected eight employees to represent the different departments and responsibilities within Wire Finance.

The next task is to turn those designed identities into actual Microsoft Entra user accounts.

But creating an account is only the beginning.

We also need to establish the attributes that will eventually determine where each employee belongs and what access they should receive.

**Next: User Creation — bringing the first eight Wire Finance employees into Microsoft Entra ID.**