---
layout: lesson
title: "Organising Access: Groups and Departments"
series: wire-finance
parent: iam-design
order: 3
description: "Wire Finance has five departments, but access cannot be managed person by person forever. Here we translate the structure of the business into groups that can actually control access."
---

Wire Finance now has different identity types and consistent naming conventions.

But there is another problem to solve:

**How do we manage access without configuring every employee individually?**

With only 25 employees, assigning permissions user by user might initially seem manageable.

Olivia gets Finance access.

Maya gets HR access.

Taylor gets security-related access.

Noah gets Sales access.

Simple enough.

Until Olivia changes role.

Or a new Finance employee joins.

Or someone moves from Sales to Operations.

Or the organisation grows.

Managing access directly against individual users quickly becomes difficult to understand, maintain, and review.

Wire Finance therefore organises access around something more predictable:

**the structure and responsibilities of the business.**

This is where groups become important.

### Start With the Organisation

Wire Finance has five departments:

| Department | Staff | Responsibility |
|---|---:|---|
| **Finance** | 5 | Accounting, payments, reporting, and financial records |
| **HR** | 2 | Employee records, recruitment, and onboarding |
| **Operations** | 7 | Business operations and customer processing |
| **Sales** | 7 | Sales, customer relationships, and account management |
| **IT** | 4 | IT administration, deployment, and security operations |

All 25 employees work for Wire Finance.

That gives every workforce identity something in common, but it does not mean everyone should receive the same access.

To reflect those similarities and differences, Wire Finance uses several group families.

### Group Naming Structure

The group name tells us what kind of relationship or purpose the group represents.

| Prefix | Purpose | Example |
|---|---|---|
| `SG-BASE-` | Organisation-wide baseline | `SG-BASE-ALL-STAFF` |
| `SG-DEPT-` | Department membership | `SG-DEPT-FINANCE-USERS` |
| `SG-ROLE-` | Job-role or functional membership | `SG-ROLE-SOC-ANALYST` |
| `SG-PILOT-` | Controlled pilot population | `SG-PILOT-IDENTITY` |
| `SG-EXCEPTION-` | Explicit access outside the normal baseline | `SG-EXCEPTION-PAYMENT-PROCESSING` |
| `SG-LICENCE-` | Licence assignment | `SG-LICENCE-M365-E5` |
| `SG-DEVICES-` | Device classification or deployment targeting | `SG-DEVICES-WINDOWS-CORPORATE` |
| `PAG-` | Privileged administrative assignment | `PAG-INTUNE-ENDPOINT-ADMIN` |

The prefixes are not security controls by themselves.

Their purpose is to make the intent of each group understandable when the same objects later appear throughout Microsoft Entra ID, Intune, Defender, and other administrative portals.

### Company Baseline

Every normal Wire Finance employee belongs to the workforce before they belong to a particular department or job role.

That relationship is represented by:

`SG-BASE-ALL-STAFF`

Conceptually:

```text
Wire Finance Employee
        ↓
SG-BASE-ALL-STAFF
        ↓
Organisation-wide baseline
```

The specific access provided by that baseline will be defined in the next part of the IAM design.

### Department Groups

Each department has its own security group.

| Department | Security Group |
|---|---|
| Finance | `SG-DEPT-FINANCE-USERS` |
| HR | `SG-DEPT-HR-USERS` |
| Operations | `SG-DEPT-OPERATIONS-USERS` |
| Sales | `SG-DEPT-SALES-USERS` |
| IT | `SG-DEPT-IT-USERS` |

For Olivia Carter, the relationship looks like this:

```text
Olivia Carter
department = Finance
        ↓
SG-DEPT-FINANCE-USERS
        ↓
Finance departmental context
```

The group becomes a bridge between **where the employee works** and the access normally associated with that part of the business.

### Department Is Not the Same as Role

Department membership tells us where someone belongs.

It does not necessarily tell us everything they do.

Taylor Reed belongs to the IT department, but Taylor's job function is SOC Analyst.

```text
                    Taylor Reed
                         │
              ┌──────────┴──────────┐
              │                     │
        IT Department           SOC Analyst
              │                     │
     SG-DEPT-IT-USERS       SG-ROLE-SOC-ANALYST
```

The department relationship answers:

**Where does Taylor belong?**

The role relationship answers:

**What does Taylor do?**

That distinction matters.

Being part of Finance does not automatically mean someone should have authority to process payments.

Being part of IT does not automatically make someone an administrator.

And being a SOC Analyst does not automatically mean Taylor's everyday employee account should hold unrestricted privileged permissions.

### Role Groups

Wire Finance therefore maintains role groups alongside department groups.

Examples include:

- `SG-ROLE-FINANCE-ANALYST`
- `SG-ROLE-HR-MANAGER`
- `SG-ROLE-OPERATIONS-SPECIALIST`
- `SG-ROLE-SALES-REPRESENTATIVE`
- `SG-ROLE-IT-ADMIN`
- `SG-ROLE-SOC-ANALYST`
- `SG-ROLE-ENDPOINT-ADMIN`
- `SG-ROLE-SECURITY-LEADERSHIP`

These groups represent job responsibilities or functional roles.

They allow Wire Finance to separate:

```text
Where someone works
        from
What someone does
```

That becomes especially important when employees change roles or take on additional responsibilities.

### Dynamic and Assigned Membership

Not every group should be populated in the same way.

For predictable organisational relationships such as company, department, and job role, Wire Finance can use trusted identity attributes to determine membership automatically.

For example:

```text
department = Finance
        ↓
Dynamic membership
        ↓
SG-DEPT-FINANCE-USERS
```

This means a person's directory attributes can drive their organisational group membership instead of an administrator repeatedly adding and removing users by hand.

Other groups require a deliberate decision.

Pilot participation, approved exceptions, licence assignment during the pilot, and privileged administrative access may need explicit membership rather than automatic inclusion.

Wire Finance therefore uses both:

**Dynamic membership**  
for relationships that can be reliably derived from trusted attributes.

**Assigned membership**  
for relationships that require deliberate authorisation or controlled targeting.

This distinction becomes increasingly important as the environment grows.

### Pilot and Exception Groups

Some access or configuration should not automatically apply to an entire department or the whole organisation.

For controlled identity testing, Wire Finance uses:

`SG-PILOT-IDENTITY`

This allows selected users to receive new identity-related configurations before wider rollout.

Exceptions are treated separately.

For example:

`SG-EXCEPTION-PAYMENT-PROCESSING`

can represent an explicitly approved capability that should not be granted to every Finance employee.

This preserves an important principle:

```text
Department membership
does not automatically equal
sensitive business authority
```

### Privileged Access Groups

Administrative access is separated from normal department and role membership.

Wire Finance uses a dedicated privileged-access group family, including:

- `PAG-ENTRA-SECURITY-READER`
- `PAG-DEFENDER-SECURITY-OPERATOR`
- `PAG-INTUNE-ENDPOINT-ADMIN`

These groups represent privileged administrative capability rather than ordinary workforce access.

Their existence reinforces an important distinction:

```text
Job responsibility
        ≠
Automatic administrative privilege
```

We will explore the controls around privileged identities and these assignments later in the IAM design.

### Licensing Groups

Licensing is also managed through a dedicated group.

Wire Finance currently uses:

`SG-LICENCE-M365-E5`

During the pilot, membership is intentionally assigned rather than automatically derived.

This gives Wire Finance explicit control over which users consume Microsoft 365 E5 licences while the environment is being validated.

As the environment matures, that approach can later be reviewed and, where appropriate, moved toward attribute-driven licensing.

### Device Groups

People are not the only directory objects that require organisation.

Wire Finance also uses groups for devices, including:

- `SG-DEVICES-WINDOWS-CORPORATE`
- `SG-DEVICES-WINDOWS-PILOT`
- `SG-DEVICES-PRIVILEGED`
- `SG-DEVICES-NONCOMPLIANT-TEST`
- `SG-DEVICES-AUTOPILOT-CORPORATE`

These groups help classify devices and target management or security configurations.

The detailed device-group logic is covered later in the **Device Provisioning Model**.

### Why Groups Matter

Without structure, Wire Finance would eventually become a directory of individual accounts with one-off permissions attached to them.

Groups give us a way to express relationships instead:

```text
Who works here?
        ↓
Company baseline

Where do they work?
        ↓
Department

What do they do?
        ↓
Role

Do they need something unusual?
        ↓
Exception

Are they performing privileged administration?
        ↓
Privileged assignment
```

The groups organise those relationships.

They still do not answer the final question:

**What access should each of those relationships actually provide?**

That is the next step.

Now that Wire Finance has a structure for organising identities, we can begin defining its **access baselines**.