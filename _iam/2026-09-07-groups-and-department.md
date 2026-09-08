---
layout: lesson
title: "Organising Access: Groups and Departments"
series: wire-finance
parent: iam-design
order: 4
description: "Wire Finance has five departments, but access cannot be managed person by person forever. Here we translate the structure of the business into groups that can actually control access."
---

Wire Finance now has different identity types, consistent naming conventions, and clear rules separating normal, privileged, and emergency access.

But there is another problem to solve:

**How do we manage access for an organisation without configuring every employee individually?**

With only 25 employees, assigning permissions user by user might seem manageable.

Olivia gets Finance access.

Maya gets HR access.

Taylor gets security-related access.

Noah gets Sales access.

Simple enough.

Until Olivia changes role.

Or a new Finance employee joins.

Or someone moves from Sales to Operations.

Or the company grows.

Managing access directly against individual users might work at the beginning, but it becomes increasingly difficult to understand, maintain, and review as the organisation changes.

Wire Finance therefore begins organising access around something much more stable:

**the structure of the business itself.**

This is where groups become important.

---

##### Start With the Organisation

Before we create groups, we first need to understand how Wire Finance is structured.

| Department | Staff | Responsibility |
|---|---:|---|
| **Finance** | 5 | Accounting, payments, reporting, and financial records |
| **HR** | 2 | Employee records, recruitment, and onboarding |
| **Operations** | 7 | Business operations and customer processing |
| **Sales** | 7 | Sales, customer relationships, and account management |
| **IT** | 4 | IT administration, deployment, and security operations |

All 25 employees work for Wire Finance.

But being employed by the same organisation does not mean they should all have access to the same information.

Finance handles financial records.

HR deals with employee information.

Operations works with business and customer-processing activities.

Sales manages customer relationships and account information.

IT supports the technology environment, deployment, and security operations.

That structure gives us our first clue about how access should be organised.

---

#### Department Groups

Wire Finance represents each department with its own security group.

| Department | Security Group |
|---|---|
| Finance | `SG-DEPT-FINANCE-USERS` |
| HR | `SG-DEPT-HR-USERS` |
| Operations | `SG-DEPT-OPERATIONS-USERS` |
| Sales | `SG-DEPT-SALES-USERS` |
| IT | `SG-DEPT-IT-USERS` |

Instead of thinking:

> Give Olivia access to these individual Finance resources.

we can begin thinking:

```text
Olivia Carter
      ↓
Finance Department
      ↓
SG-DEPT-FINANCE-USERS
      ↓
Finance baseline access

```

The group becomes the bridge between **where the employee works** and **the access normally required by that department**.

If another Finance employee joins later, we do not need to recreate Olivia's permissions manually.

We can place the new employee into the appropriate group and allow the access model to do the rest.

##### Groups Have Different Purposes

Department groups are only one part of the design.

Wire Finance also defines groups for roles, devices, pilot deployments, and licensing.

| Group Type | Examples |
|---|---|
| **Department** | `SG-DEPT-FINANCE-USERS`, `SG-DEPT-HR-USERS` |
| **Role-related** | `SG-ROLE-SOC-ANALYST`, `SG-ROLE-ENDPOINT-ADMIN` |
| **Device** | `SG-DEVICES-WINDOWS-CORPORATE`, `SG-DEVICES-PRIVILEGED` |
| **Pilot** | `SG-PILOT-IDENTITY`, `SG-PILOT-INTUNE`, `SG-PILOT-DEFENDER` |
| **Licensing** | `SG-LICENCE-PILOT-USERS`, `SG-LICENCE-SECURITY-USERS` |

Each category answers a different question.

**Department groups** tell us where someone belongs.

**Role groups** help represent what they actually do.

**Device groups** allow systems to be organised according to their purpose or state.

**Pilot groups** give us a controlled way to test new configurations before wider deployment.

**Licensing groups** help organise which users should receive particular services or capabilities.

This means groups are not simply containers for users.

They begin to represent the structure, responsibilities, devices, and deployment decisions that exist across Wire Finance.

---

##### Department Is Not the Same as Role

This distinction is important.

Taylor Reed belongs to the IT department, but Taylor's job function is SOC Analyst.

Those are two different relationships.

```text
                    Taylor Reed
                         │
              ┌──────────┴──────────┐
              │                     │
        IT Department           SOC Analyst
              │                     │
     SG-DEPT-IT-USERS      SG-ROLE-SOC-ANALYST
```

Department membership tells us **where Taylor belongs**.

Role membership tells us **what Taylor does**.

Being part of IT may provide access to standard IT departmental resources.

Being a SOC Analyst may require additional access specifically related to security investigation and response.

The same principle applies elsewhere.

Working in Finance does not automatically mean someone should have payment authority.

And being part of IT does not automatically mean someone should receive privileged administrative access.

Groups give us structure.

But we still need to decide **what access those groups should actually provide**.

---

#### Device Groups

People are not the only objects that need organisation.

Wire Finance also defines groups for devices.

Examples include:

- `SG-DEVICES-WINDOWS-CORPORATE`
- `SG-DEVICES-WINDOWS-PILOT`
- `SG-DEVICES-PRIVILEGED`
- `SG-DEVICES-NONCOMPLIANT-TEST`

This allows devices to be classified according to their purpose or state.

A standard corporate laptop and a privileged workstation should not necessarily be treated the same way.

Likewise, a test or pilot device may need to receive a configuration before that configuration is introduced to the wider organisation.

These groups will become particularly useful when Wire Finance begins implementing endpoint management, compliance, and security controls.

---

#### Pilot Groups

Some changes are too important to deploy to the entire organisation immediately.

Wire Finance therefore creates dedicated pilot groups such as:

- `SG-PILOT-IDENTITY`
- `SG-PILOT-INTUNE`
- `SG-PILOT-DEFENDER`
- `SG-PILOT-CONDITIONAL-ACCESS`

Imagine we are preparing to introduce a new Conditional Access policy.

Rather than applying an untested configuration to every employee at once, we can begin with:

```text
New Conditional Access Policy
            ↓
SG-PILOT-CONDITIONAL-ACCESS
            ↓
       Test and Validate
            ↓
      Wider Deployment
```

This gives Wire Finance a controlled way to introduce changes and observe their impact before wider deployment.

---

#### Licensing Groups

Wire Finance also defines groups for organising licences and services.

Examples include:

- `SG-LICENCE-PILOT-USERS`
- `SG-LICENCE-SECURITY-USERS`
- `SG-LICENCE-ENDPOINT-USERS`

Again, the goal is to move away from managing everything user by user.

Instead of asking:

**Which individual users should I manually assign this service to?**

we can begin asking:

**Which group represents the users who require this service?**

That difference becomes increasingly valuable as the environment grows.

---

#### From Groups to Entitlements

At this point, the Wire Finance identity model is beginning to develop several layers.

```text
Employee
   │
   ├── Company access
   │
   ├── Department access
   │      └── SG-DEPT-*
   │
   ├── Role-specific access
   │      └── SG-ROLE-*
   │
   ├── Pilot membership
   │      └── SG-PILOT-*
   │
   ├── Device classification
   │      └── SG-DEVICES-*
   │
   └── Licensing
          └── SG-LICENCE-*
```

This is much more meaningful than simply having a directory containing 25 unrelated user accounts.

The identity begins to reflect the employee's place within the organisation.

But groups alone still do not answer our original question:

**Who should have access to what — and why?**

We now need to look at the actual people inside Wire Finance, the roles they perform, and the access each role should receive.

That is where our identity structure begins turning into an **access model**.

**Who are the key users inside Wire Finance, and what should their roles actually entitle them to access?**