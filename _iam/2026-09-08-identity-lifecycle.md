---
layout: lesson
title: "Identity Lifecycle"
chapter: 5
series: wire-finance
show_on_master: true
nav_id: identity-lifecycle
children_heading: "Explore the Identity Lifecycle"
author: Cybernetswork
image: assets/images/active-directory/identity-lifecycle.png
description: "Access should change when people do. Follow how Wire Finance manages identities as employees join, move through the organisation, and eventually leave."
---

Wire Finance now has a defined access model.

We know what every employee should receive through the company-wide baseline.

We know how departments and job roles add additional access.

We also know how approved exceptions and privileged access should be handled when someone requires permissions outside the normal model.

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

### The Identity Lifecycle

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

If someone moves from Sales to Operations, their old Sales access should not remain simply because nobody remembered to remove it.

If an employee temporarily receives additional access, that permission should not quietly become permanent.

And when somebody leaves Wire Finance, disabling their standard account alone may not be enough.

Their privileged identities, group memberships, application permissions, active sessions, licences, exception access, devices, and ownership of business resources may all need attention.

### Access Must Change When the Person Does

The access baselines we defined earlier give Wire Finance a picture of what **normal access** should look like.

Identity lifecycle management keeps that picture accurate over time.

Consider Noah Williams.

Noah begins as a Sales Representative:

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

If Wire Finance handled every move this way, access would accumulate each time an employee changed responsibility.

Instead, the organisation needs to ask:

- What access is still required?
- What access is no longer justified?
- What new access is required?
- Does anything sensitive require fresh approval?
- Does any privileged or exception access need to be removed or reassessed?

The objective is not simply to add whatever the employee needs next.

It is to **recalculate what the identity should look like now**.

That is the difference between adding permissions and actually managing an identity lifecycle.

### Lifecycle Changes Need a Business Trigger

Identity changes should not begin simply because somebody asks an administrator to change an account.

There should be an approved business event behind the change.

Examples include:

```text
Approved Hire
      ↓
Joiner Process

Approved Role or Department Change
      ↓
Mover Process

Confirmed End of Employment
      ↓
Leaver Process
```

HR, management, IT, and security may contribute different information, but the technical identity should reflect an approved business decision.

This gives Wire Finance a clearer chain between:

```text
Business Event
      ↓
Identity Change
      ↓
Access Change
      ↓
Validation
```

That relationship becomes especially important when the change affects sensitive or privileged access.

### More Than Account Creation and Deletion

Identity lifecycle management involves far more than creating an account when someone arrives and disabling it when they leave.

Across the three stages, Wire Finance may need to manage:

- identity attributes
- department membership
- role membership
- security groups
- applications
- data access
- licences
- devices
- authentication methods
- exception access
- privileged identities and permissions
- active sessions
- business-resource ownership
- final access validation

Different people inside Wire Finance may contribute to different parts of the process.

HR provides authoritative employment information.

Managers define the employee's business responsibilities.

IT creates or modifies identities, groups, licences, applications, and devices.

Security reviews elevated or sensitive access where required.

The SOC may become involved where monitoring, investigation, or security review is necessary.

Identity lifecycle management therefore connects **people, business processes, access control, endpoint management, and security operations**.

### The Joiner Stage

A Joiner is someone entering the organisation.

The objective is not simply to create an account.

Wire Finance needs to make sure the new employee receives the correct identity, attributes, baseline access, department and role relationships, licence, authentication setup, and device without receiving unnecessary permissions.

Conceptually:

```text
Approved Hire
      ↓
Identity Created
      ↓
Attributes Established
      ↓
Baseline + Department + Role Access
      ↓
Licence and Device
      ↓
Authentication Setup
      ↓
Access Validated
```

The detailed Joiner process is covered in the next part of the lifecycle.

### The Mover Stage

A Mover is an existing employee whose relationship with the organisation changes.

That might mean:

- changing department
- changing job role
- taking on new responsibilities
- losing responsibilities
- gaining temporary access
- gaining or losing privileged duties

The key principle is that a move should trigger **reassessment**, not simple accumulation.

```text
Previous Access
      ↓
Review
      ↓
Remove What Is No Longer Required
      ↓
Add Newly Approved Access
      ↓
Validate the New State
```

The detailed Mover process will show how Wire Finance handles these changes without allowing old access to linger indefinitely.

### The Leaver Stage

A Leaver is someone whose relationship with Wire Finance is ending.

This stage needs to do more than disable a user account.

Depending on the employee, Wire Finance may need to:

- block sign-in
- revoke active sessions
- remove normal group memberships
- remove exception access
- disable or remove privileged identities
- remove administrative assignments
- reclaim licences
- recover or retire devices
- transfer ownership of business resources
- preserve required organisational data
- validate that access has actually been removed

The objective is simple:

**employment may end, but access must not survive accidentally.**

The detailed Leaver process will define how Wire Finance closes the identity safely.

### From Arrival to Departure

The goal of identity lifecycle management is straightforward:

**give people the access they need when they need it, change that access when their responsibilities change, and remove it when the business relationship ends.**

Wire Finance will manage that journey through three connected processes:

```text
JOINER
Who is this person,
and what should they receive?
        │
        ▼
MOVER
What has changed,
and what should their access become?
        │
        ▼
LEAVER
What must be removed,
recovered, transferred, or preserved?
```

Each stage solves a different problem.

Together, they keep the identity aligned with the person behind it throughout the employee's relationship with Wire Finance.