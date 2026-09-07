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

**Shared services may need identities.**

And if the normal administrative path ever fails, the organisation may need an identity specifically designed for recovery.

Before Wire Finance can decide **who should have access to what**, we first need to understand the different types of identities that will exist in the environment.

##### Identity Type in Wire Finance

| Identity Type | Purpose | Example |
|---|---|---|
| **Standard User** | Everyday work | `john.doe@wirefinance.com` |
| **Privileged Administrator** | Administrative activity only | `adm-john.doe@wirefinance.com` |
| **Emergency Access** | Tenant recovery | `emergency-access-01@wirefinance.com` |
| **Guest** | External partners or contractors | External Entra B2B user |
| **Shared / Resource Identity** | Shared mailbox or functional service | `finance@wirefinance.com` |
| **Workload Identity** | Applications and automation | Managed identity or service principal |

The important thing here is that these identities may exist inside the same organisation, but they do not serve the same purpose and should not automatically be treated the same way.

##### Standard User Identity

The standard user identity is the account an employee uses for normal day-to-day work.

This is the identity used for activities such as accessing company resources, working with Microsoft 365 services, collaborating with colleagues, and performing normal job responsibilities.

For example:

`john.doe@wirefinance.com`

The key idea is that a standard account represents the employee in their normal working capacity.

It should not automatically receive elevated administrative privileges simply because the employee also performs administrative work.

##### Privileged Administrator Identity

Some employees will need elevated permissions to manage systems, identities, endpoints, or security services.

Wire Finance separates this administrative activity from normal productivity work by assigning a separate privileged identity.

For example:

`adm-john.doe@wirefinance.com`

The administrator may therefore have two identities:

- `john.doe@wirefinance.com` for everyday work
- `adm-john.doe@wirefinance.com` for approved administrative activity

This separation makes an important distinction between **being an employee** and **acting as an administrator**.

We will explore privileged identities in more detail later in the IAM design.

##### Emergency Access Identity

What happens if normal administrative access stops working?

Perhaps an authentication problem occurs, administrators become locked out, or a configuration change prevents the usual privileged identities from accessing the environment.

Wire Finance therefore includes dedicated emergency access identities.

For example:

`emergency-access-01@wirefinance.com`

These accounts are not designed for normal administration.

Their purpose is recovery.

They provide the organisation with a controlled way to regain access when the usual administrative path is unavailable.

##### Guest Identity

Not everyone who needs access to Wire Finance will necessarily be an employee.

External partners, suppliers, consultants, or contractors may need access to selected company resources without becoming normal internal users.

These identities are represented as **Guest identities**, such as external Microsoft Entra B2B users.

The important distinction is that the user exists outside Wire Finance but has been granted access to specific resources within the organisation.

Their access therefore needs to be intentionally limited to what the external relationship requires.

##### Shared and Resource Identities

Some identities represent a shared business function rather than an individual person.

For example:

`finance@wirefinance.com`

This might represent a shared mailbox or another functional resource used by a team.

Unlike a named employee identity, the purpose of the identity is tied to a business function or shared service.

This becomes important when we later consider ownership, permissions, and what happens when individual employees join, move, or leave the organisation.

##### Workload Identities

Not just people alone need to authenticate.

Applications and automated processes may also need to access cloud resources and services.

Wire Finance therefore recognises **Workload Identities**, such as:

- Managed identities
- Service principals

These identities allow applications and automation to operate without pretending to be a human user.

As the Wire Finance environment grows, workload identities will become increasingly important because cloud services, scripts, automation, and integrations will all need controlled ways to access resources.

##### Why Separate Identity Types?

Imagine if every identity inside Wire Finance were simply treated as a normal user.

An administrator could use the same identity for email and privileged activity.

An application might rely on a normal employee account.

External users could become difficult to distinguish from employees.

Emergency accounts could accidentally become part of everyday administration.

That would make access much harder to understand, control, and eventually audit.

By defining identity types early, Wire Finance establishes a clearer foundation for the access decisions that come next.

Before we decide what an identity can access, we first need to understand **what kind of identity it is and why it exists**.

And now that we know who — or what — may need access, the next question becomes:

**How should we name and identify them consistently as the organisation grows?**