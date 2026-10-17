---
layout: lesson
title: "Privileged and Emergency Access"
series: wire-finance
parent: iam-design
order: 5
description: Some identities can change the environment itself. Wire Finance separates privileged access from everyday work — and keeps a controlled recovery path for when normal administration fails.
---

Not every identity inside Wire Finance carries the same level of risk.

A standard employee account may access email, collaboration tools, and the resources required for day-to-day work.

A privileged identity is different.

It may be capable of changing configurations, managing devices, administering identities, investigating security incidents, or affecting other users.

The question is therefore no longer simply:

**What should this person be allowed to access?**

It also becomes:

**How do we make sure powerful access is only available to the right identity, for the right reason, and when it is genuinely needed?**

There is another problem to consider.

What happens if the administrators responsible for the environment can no longer get in?

Wire Finance therefore treats **privileged access** and **emergency access** as two related but distinct parts of its identity design.

### Privileged Access Is Not the Same as Job Responsibility

Earlier in the IAM design, we created role groups such as:

- `SG-ROLE-IT-ADMIN`
- `SG-ROLE-SOC-ANALYST`
- `SG-ROLE-ENDPOINT-ADMIN`
- `SG-ROLE-SECURITY-LEADERSHIP`

These groups describe what an employee does.

They do not automatically mean the employee's everyday account should receive administrative privileges.

For example:

```text
Taylor Reed
     │
     ▼
SOC Analyst
     │
     ▼
SG-ROLE-SOC-ANALYST
     │
     ▼
Business responsibility identified
```

Taylor's role explains why security capabilities may be required.

It does not mean:

```text
taylor.reed
     =
Privileged Administrator
```

Wire Finance therefore keeps the employee's business role and administrative authority separate.

### Separate Privileged Identities

Employees who require administrative access use a separate privileged identity.

For example:

```text
jordan.lee@wirefinance.com
```

represents Jordan Lee during normal day-to-day work.

A separate identity such as:

```text
adm-jordan.lee@wirefinance.com
```

is reserved for approved administrative activity.

Conceptually:

```text
Jordan Lee
      │
      ├── Standard Identity
      │      └── Everyday work
      │
      └── Privileged Identity
             └── Approved administration
```

The same principle applies to other members of the IT and security team.

Normal productivity and administrative activity should not be performed through the same identity simply because the same person performs both responsibilities.

This reduces the amount of time a highly privileged identity is exposed to activities such as email, collaboration, and general web browsing.

### Privileged Identity Rules

Wire Finance applies several principles to privileged identities.

- Privileged users receive a separate administrative identity.
- Administrative identities are not used for routine email, Teams, web browsing, or everyday productivity.
- Privileged identities receive only the permissions required for their approved responsibilities.
- Administrative capability is assigned separately from normal department and role membership.
- Global Administrator is not used for routine administration where a less privileged role is sufficient.
- Privileged activity must remain attributable and auditable.
- Administrative access should be reviewed periodically.
- Where supported and appropriate, privileged access should become eligible or time-bound rather than permanently active.

The principle is simple:

**administrative access should be deliberate, limited, and visible.**

### From Job Role to Administrative Permission

Wire Finance separates several concepts that can otherwise become easy to confuse.

```text
Employee
   │
   ▼
Business Role
SG-ROLE-*
   │
   │ explains why privilege may be needed
   │
   ▼
Privileged Identity
adm-*
   │
   ▼
Privileged Access Group
PAG-*
   │
   ▼
Approved Administrative Permission
```

Each layer has a different purpose.

The **role group** describes what the employee does.

The **privileged identity** is the account used when performing administrative work.

The **Privileged Access Group**, or `PAG-*`, represents an approved administrative capability.

The platform role or permission ultimately determines what the privileged identity can actually do.

This structure prevents Wire Finance from treating:

```text
Works in IT
```

as if it automatically means:

```text
Has administrative authority
```

### Privileged Access Groups

Wire Finance uses a dedicated `PAG-` naming family for privileged administrative assignments.

Initial examples include:

| Privileged Access Group | Administrative Purpose |
|---|---|
| `PAG-ENTRA-SECURITY-READER` | Read security-related information within Microsoft Entra |
| `PAG-DEFENDER-SECURITY-OPERATOR` | Perform approved security investigation and response activities |
| `PAG-INTUNE-ENDPOINT-ADMIN` | Perform approved endpoint-management administration |

These groups are deliberately separated from normal workforce groups such as:

`SG-DEPT-IT-USERS`

and:

`SG-ROLE-SOC-ANALYST`

That separation allows Wire Finance to answer two different questions.

```text
What does this employee do?
        ↓
SG-ROLE-*

What administrative authority has been approved?
        ↓
PAG-*
```

The answers may be related, but they are not the same thing.

### Initial Privileged Responsibilities

Several Wire Finance roles may require elevated capability.

| Role | Administrative Requirement |
|---|---|
| **Head of Security Operations** | Security leadership and controlled high-level security administration |
| **IT Administrator** | Approved tenant and IT administration |
| **SOC Analyst** | Security investigation and response |
| **Deployment / Support Engineer** | Endpoint deployment and Intune administration |

These employees all belong to the IT department, but they do not require the same permissions.

Taylor Reed, as SOC Analyst, may require investigation capabilities without needing broad tenant administration.

Casey Ng, as Deployment / Support Engineer, may require endpoint-management capabilities without needing unrestricted security administration.

Alex Morgan, as IT Administrator, may require broader IT administration without automatically receiving every security role.

Jordan Lee, as Head of Security Operations, may require higher-level security administration, but even that access should remain controlled rather than becoming unrestricted by default.

This is separation of duties applied to real identities rather than left as an abstract principle.

### Privileged Access Should Not Always Mean Permanent Access

Some administrative capability may be required regularly.

Other privileges may only be required occasionally.

Wire Finance therefore intends to use privileged-access controls that allow administrative authority to become **eligible, activated when needed, and reviewed**, rather than assuming every privilege must remain permanently active.

Conceptually:

```text
Privileged Identity
        │
        ▼
Eligible Administrative Access
        │
        ▼
Approved Activation
        │
        ▼
Temporary Active Privilege
        │
        ▼
Access Expires
```

Microsoft Entra Privileged Identity Management will later help us implement this model where supported.

The detailed activation settings, approval rules, time limits, and implementation steps belong in the implementation phase.

At the design stage, the principle is what matters:

**privileged access should exist because it is required now, not simply because it was required once.**

### Emergency Access

Controlled privileged access solves normal administration.

It does not solve every failure scenario.

What happens if the normal privileged path itself stops working?

Possible situations include:

- a Conditional Access configuration locks out administrators
- normal privileged identities become unavailable
- an authentication dependency fails
- privileged-access activation cannot be completed
- an administrator accidentally removes or changes critical access
- a wider identity incident prevents normal administration

Wire Finance should not discover its recovery problem only after every normal administrator is already locked out.

Dedicated emergency access identities therefore provide an independent recovery path.

Examples include:

```text
emergency-access-01@wirefinance.com
emergency-access-02@wirefinance.com
```

These identities exist for one purpose:

**tenant recovery when normal administrative access is unavailable.**

They are not normal administrator accounts.

They are not productivity identities.

And they should never become a convenient shortcut around the privileged-access model.

### Emergency Access Design Principles

An emergency identity needs different safeguards from a normal administrator account because it must remain usable during failures affecting the normal administrative path.

Wire Finance therefore designs emergency access around several principles.

- More than one emergency identity should exist so recovery does not depend on a single account.
- Emergency identities should remain independent of normal employee accounts.
- They should remain independent of future on-premises Active Directory or hybrid identity dependencies.
- Their authentication method should be securely controlled and resilient.
- They should not be used for email, collaboration, or routine administration.
- Their credentials or authentication material should be stored securely and available only through an authorised recovery process.
- Their use should be heavily monitored.
- Any emergency sign-in should be treated as a significant security event.
- Emergency access should be tested periodically so Wire Finance knows the recovery path still works.
- After genuine emergency use, the organisation should review what happened and rotate or re-secure the emergency credentials where appropriate.

There is an important balance here.

Emergency accounts must be protected strongly, but they also cannot depend entirely on the same controls they are intended to recover from.

Otherwise the organisation could create a perfectly secured emergency account that is unavailable during the emergency.

### Emergency Access Is Not an Exception Group

Earlier, Wire Finance defined exception access for legitimate requirements outside a normal employee baseline.

For example:

`SG-EXCEPTION-PAYMENT-PROCESSING`

Emergency access is different.

An exception group grants an approved business capability.

An emergency identity exists to recover administrative control of the environment.

```text
Exception Access
        =
Additional approved business access

Privileged Access
        =
Approved administrative authority

Emergency Access
        =
Recovery when normal administration fails
```

These concepts should not be mixed together.

### Privileged Access vs Emergency Access

Both privileged and emergency identities may hold powerful capabilities, but they exist for different reasons.

| Identity Type | Purpose | Normal Use |
|---|---|---|
| **Standard User** | Everyday employee activity | Daily |
| **Privileged Administrator** | Perform approved administrative responsibilities | Only when administration is required |
| **Emergency Access** | Recover control when normal administration is unavailable | Emergency use only |

A privileged administrator identity is part of normal controlled administration.

An emergency identity exists for exceptional recovery.

If an emergency account becomes part of normal administration, it stops functioning as a genuine emergency control.

### The Privileged Access Model

Wire Finance can now separate three very different relationships.

```text
Everyday Work
     │
     ▼
Standard Identity
firstname.lastname
     │
     ▼
Company + Department + Role
+ Approved Exceptions
```

Administrative activity follows a different path:

```text
Administrative Responsibility
     │
     ▼
Separate Privileged Identity
adm-firstname.lastname
     │
     ▼
Approved PAG Membership
     │
     ▼
Required Administrative Permission
```

And recovery sits outside both:

```text
Normal Administrative Path Unavailable
     │
     ▼
Emergency Access Identity
     │
     ▼
Tenant Recovery
```

This makes the purpose of each identity much easier to understand.

A standard identity represents the employee.

A privileged identity represents the employee acting with approved administrative authority.

An emergency identity exists to restore control when the normal administrative model cannot be used.

### Why This Separation Matters

Wire Finance now has a clear boundary between **being an employee** and **acting as an administrator**.

It also has a recovery path for situations where normal administration fails.

That gives us a much cleaner answer when we ask:

**Who has power inside the environment, why do they have it, and when should that power be available?**

But designing access is only half of the problem.

People do not remain static.

They join Wire Finance.

They change departments.

They take on new responsibilities.

They may gain privileged duties.

And eventually, they leave.

When the person changes, their access needs to change with them.

The next part of the journey therefore moves from **access design** to **identity lifecycle**.

**How should Wire Finance manage access from the moment someone joins the organisation until the day they leave?**