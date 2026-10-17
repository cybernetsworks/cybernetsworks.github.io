---
layout: lesson
title: "Identity Types"
series: wire-finance
parent: iam-design
order: 1
description: Not every identity inside Wire Finance belongs to an employee. Before assigning access, we first need to understand who — or what — is requesting it.
---

When we hear the word *identity*, the first thing that usually comes to mind is a user account.

Someone joins the organisation, receives an email address, signs into a laptop, and begins working.

But inside a modern organisation, identities represent much more than employees.

**Applications need identities.**

**Administrators need identities.**

**External partners may need identities.**

**Shared business resources may have identities.**

And if the normal administrative path ever fails, the organisation may need an identity designed specifically for recovery.

Before Wire Finance can decide **who should have access to what**, we first need to understand the different types of identities that exist in the environment.

> This lesson focuses on human, administrative, collaborative, and workload identities. Devices also receive identities and contribute to access decisions, but device identity is treated separately in the Device Provisioning Model.

### Identity Types in Wire Finance

| Identity Type | Purpose | Example |
|---|---|---|
| **Standard User** | Everyday employee activity | `olivia.carter@wirefinance.com` |
| **Privileged Administrator** | Approved administrative activity only | `adm-jordan.lee@wirefinance.com` |
| **Emergency Access** | Tenant recovery when normal administration is unavailable | `emergency-access-01@wirefinance.com` |
| **Guest** | Controlled access for external collaborators | External Microsoft Entra B2B user |
| **Shared / Resource Identity** | Represents a shared business function or resource | `finance@wirefinance.com` |
| **Workload Identity** | Allows applications and automation to access resources | Managed identity or service principal |

These identities may exist inside the same organisation, but they do not serve the same purpose and should not automatically be governed in the same way.

### Standard User Identity

The standard user identity is the account an employee uses for normal day-to-day work.

For example:

`olivia.carter@wirefinance.com`

Olivia uses this identity to access company resources, work with Microsoft 365 services, collaborate with colleagues, and perform her normal responsibilities as a Finance Analyst.

The key idea is that a standard account represents an employee in their everyday working capacity.

It should not automatically receive elevated administrative privileges simply because the employee may also perform administrative responsibilities.

### Privileged Administrator Identity

Some employees need elevated permissions to manage identities, endpoints, security services, or other parts of the environment.

Wire Finance separates that administrative activity from normal productivity work by using a separate privileged identity.

Jordan Lee, for example, may have:

- `jordan.lee@wirefinance.com` for everyday work
- `adm-jordan.lee@wirefinance.com` for approved administrative activity

This creates an important distinction between **being an employee** and **acting as an administrator**.

We will explore privileged identities and their controls in more detail later in the IAM design.

### Emergency Access Identity

What happens if normal administrative access stops working?

An authentication failure, configuration mistake, or access-control problem could prevent normal privileged identities from reaching the tenant.

Wire Finance therefore maintains dedicated emergency access identities, such as:

`emergency-access-01@wirefinance.com`

These accounts are not designed for routine administration.

Their purpose is recovery: providing a controlled way to regain access when the normal administrative path is unavailable.

### Guest Identity

Not everyone who needs access to Wire Finance is necessarily part of its internal workforce.

External partners, suppliers, or consultants may need controlled access to selected company resources without becoming normal internal users.

Microsoft Entra B2B guest identities provide a way to represent those external collaborators.

The important distinction is that the person belongs outside Wire Finance but has been granted access to specific resources inside the organisation.

Their access should therefore be intentionally limited to what the external relationship requires.

### Shared and Resource Identities

Some resources represent a shared business function rather than an individual employee.

For example:

`finance@wirefinance.com`

This could represent a Finance shared mailbox used by several authorised employees.

Unlike a named employee identity, its purpose is tied to a business function rather than one person.

Where possible, employees should access shared resources through their own named identities rather than signing directly into a common account. This preserves accountability and makes it easier to understand who performed an action.

That distinction becomes important when we later consider ownership, permissions, and what happens when employees join, move, or leave the organisation.

### Workload Identities

People are not the only things that need to authenticate.

Applications, scripts, automation, and cloud services may also need controlled access to resources.

Wire Finance therefore recognises workload identities such as:

- managed identities
- service principals

These allow applications and automation to authenticate without pretending to be a human employee.

As the environment grows, workload identities will become increasingly important because integrations and automated processes will need their own controlled and auditable access.

### Why Separate Identity Types?

Imagine if every identity inside Wire Finance were treated like a normal user.

An administrator could use the same identity for email and privileged activity.

An application might rely on an employee account.

External collaborators could become difficult to distinguish from the internal workforce.

Emergency access identities could accidentally become part of everyday administration.

Shared accounts could make it difficult to determine who actually performed an action.

Access would quickly become harder to understand, control, and audit.

By defining identity types early, Wire Finance establishes a clearer foundation for the access decisions that come next.

Before we decide what an identity can access, we first need to understand **what kind of identity it is and why it exists**.

Now that we know who — or what — may need access, the next question becomes:

**How should we name and identify them consistently as the organisation grows?**