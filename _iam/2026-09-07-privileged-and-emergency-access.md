---
layout: lesson
title: "Privileged and Emergency Access"
series: wire-finance
parent: iam-design
order: 3
description: Some identities can change the environment itself. Wire Finance separates privileged access from everyday work — and keeps a controlled recovery path for when normal administration fails.
---


Not every identity inside Wire Finance carries the same level of risk.

A standard employee account may access email, collaboration tools, and resources needed for day-to-day work.

A privileged identity is different.

It may be capable of changing configurations, administering systems, managing security controls, or affecting other users.

That means the question is no longer simply:

**What should this user be allowed to access?**

It becomes:

**How do we make sure powerful access is only used when it is genuinely needed?**

And there is another problem to consider.

What happens if the people responsible for administering the environment can no longer get in?

Wire Finance therefore treats **privileged access** and **emergency access** as two related but distinct parts of its identity design.

---

##### Privileged Access

Wire Finance separates normal employee activity from administrative activity.

A user who requires privileged access receives a separate administrative identity rather than using their everyday productivity account for both purposes.

For example:

`john.doe@wirefinance.com`

may be used for normal work, while:

`adm-john.doe@wirefinance.com`

is reserved for approved administrative activity.

This separation helps ensure that elevated permissions are not unnecessarily attached to the same identity used for activities such as email or web browsing.

##### Privileged Account Rules

Wire Finance defines the following rules for privileged identities:

- Privileged users receive a separate administrative identity.
- Administrative identities are not used for normal email or web browsing.
- Privileged access follows the principle of least privilege.
- Administrative roles are assigned according to job responsibility.
- Privileged activity must be auditable.
- Global Administrator is not used for routine administration.
- Privileged access is reviewed periodically.

These rules establish an important principle:

**Administrative access should be deliberate, limited, and visible.**

Having an administrative role does not mean an identity should carry unrestricted permissions all the time.

The level of access should reflect the responsibility being performed.

##### Initial Role Separation

Wire Finance already has several roles that may require elevated access, but their responsibilities are different.

| Role | Privileged Responsibility |
|---|---|
| **Head of SecOps** | Security leadership and controlled high-level administration |
| **IT Administrator** | IT and tenant administration |
| **SOC Analyst** | Security investigation and response |
| **Deployment / Support Engineer** | Intune and endpoint deployment |

The important point is that these users all work within IT and security, but they do not automatically require the same administrative permissions.

A SOC Analyst investigating security activity has a different responsibility from an IT Administrator managing the tenant.

Likewise, someone responsible for endpoint deployment should not automatically receive every security administration privilege available in the environment.

This is where separation of duties begins to move from a design principle into something we can actually implement.

---

#### Emergency Access

Privileged identities give administrators the access needed to manage the environment.

But Wire Finance also needs to consider what happens when the **normal privileged path stops working**.

Possible situations include:

- Conditional Access misconfiguration
- Administrator lockout
- Authentication-service failure
- Loss of normal privileged access

An organisation does not want to discover its recovery problem only after every normal administrator has already been locked out.

Wire Finance therefore defines dedicated emergency access identities.

For example:

`emergency-access-01@wirefinance.com`

These identities exist for one purpose:

**tenant recovery when normal administrative access is unavailable.**

They are not intended to become another convenient administrative account.

Their existence provides Wire Finance with a controlled recovery path for situations where the standard privileged identities cannot be used.

---

#### Privileged Access vs Emergency Access

Although both identity types may provide powerful access, they exist for different reasons.

| Identity | Purpose |
|---|---|
| **Privileged Administrator** | Perform approved administrative responsibilities |
| **Emergency Access** | Recover access when normal administrative paths fail |

A privileged account is part of normal controlled administration.

An emergency identity exists for exceptional circumstances.

That distinction matters.

If emergency access becomes part of everyday administration, it stops being an emergency control.

---

#### Why This Separation Matters

At this point, Wire Finance has started creating clear boundaries between different kinds of access.

A normal employee identity represents everyday work.

A privileged identity represents approved administrative activity.

An emergency identity represents recovery.

Each one exists for a specific reason.

And the more clearly those purposes are separated, the easier it becomes to understand who has power inside the environment, why they have it, and when it should be used.

But individual accounts are only part of the problem.

As Wire Finance grows, assigning access one user at a time would quickly become difficult to manage.

So the next question becomes:

**How can we organise users and access in a way that scales with the business?**