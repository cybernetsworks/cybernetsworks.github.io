---
layout: lesson
title: "Identity Lifecycle"
chapter: 5
series: wire-finance
show_on_master: true
nav_id: identity-lifecycle
children_heading: "Explore the Identity Lifecycle"
author: Cybernetswork
image: assets/images/active-directory/profile.png
description: "Access should change when people do. Follow how Wire Finance manages identities as employees join, move through the organisation, and eventually leave."
---

Wire Finance now has a defined access model.

We know what every employee should receive through the company-wide baseline.

We know how departments and job roles add additional access.

We also know how exceptional and privileged access should be handled when someone needs permissions outside the normal model.

But there is one thing our access design cannot assume:

**people stay the same forever.**

Employees join the organisation.

They change jobs.

They move between departments.

Their responsibilities grow or shrink.

Some receive privileged responsibilities.

Others lose access they previously required.

And eventually, people leave.

An identity that was correctly configured six months ago may no longer represent what that person should be able to access today.

That is why identity management cannot stop after an account is created.

It has to follow the person throughout their relationship with the organisation.

## The Identity Lifecycle

Wire Finance organises this journey into three stages:

| Lifecycle Stage | The Question We Need to Answer |
|---|---|
| **Joiner** | What does this person need when they enter the organisation? |
| **Mover** | What should change when their role, department, or responsibilities change? |
| **Leaver** | What must happen to their access when their relationship with the organisation ends? |

Together, these stages form the **Joiner–Mover–Leaver lifecycle**.

```text
Joiner
   │
   ▼
Identity Created
Access Assigned
Device Prepared
Authentication Registered
   │
   ▼
Mover
   │
   ▼
Access Reviewed
Old Access Removed
New Access Added
Responsibilities Updated
   │
   ▼
Leaver
   │
   ▼
Access Removed
Sessions Revoked
Ownership Transferred
Identity Disabled
```

The important idea is that access should follow the employee's **current business need**, not their history.

If someone moves from Sales to Operations, their old Sales access should not simply remain because nobody remembered to remove it.

If an employee temporarily receives additional access, that permission should not quietly become permanent.

And when somebody leaves Wire Finance, disabling their standard account alone may not be enough. Their privileged identities, group memberships, application permissions, active sessions, licences, exception access, and ownership of business resources may all need attention.

## Access Must Change When the Person Does

The access baselines we defined earlier give Wire Finance a picture of what **normal access** should look like.

Identity lifecycle management keeps that picture accurate over time.

Consider a simple example.

```text
Noah Williams
Sales Representative
      │
      ▼
Company Baseline
      +
Sales Department Baseline
      +
Sales Representative Role Baseline
```

Now imagine Noah moves into Operations.

The wrong approach would be:

```text
Keep Sales Access
      +
Add Operations Access
```

Access would accumulate every time the employee changed responsibility.

Instead, Wire Finance needs to ask:

- What access is still required?
- What access is no longer justified?
- What new access is required?
- Does anything sensitive need new approval?

That is the difference between simply **adding permissions** and actually **managing an identity lifecycle**.

## More Than Account Creation and Deletion

The lifecycle also involves more than creating an account when someone arrives and disabling it when they leave.

Across the three stages, Wire Finance may need to manage:

- Identity information
- Department membership
- Role membership
- Security groups
- Applications
- Data access
- Licences
- Devices
- MFA and authentication methods
- Exception access
- Privileged access
- Active sessions
- Business-resource ownership
- Final access validation

Different teams may also contribute to different parts of the process.

HR may provide employment information.

Managers define business requirements.

IT and Identity teams create and modify access.

Security reviews elevated or sensitive permissions.

Endpoint teams prepare devices.

And the SOC may become involved where monitoring or security review is required.

Identity lifecycle management therefore connects **people, business processes, access control, and security operations**.

## From Arrival to Departure

The goal is straightforward:

**Give people the access they need when they need it, change that access when their responsibilities change, and remove it when the business relationship ends.**

Wire Finance will manage that journey through three connected processes:

**Joiner.**

**Mover.**

**Leaver.**

Each stage has a different problem to solve — and a different set of controls needed to keep the identity aligned with the person behind it.