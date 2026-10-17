---
layout: lesson
title: "Access Baselines"
series: wire-finance
parent: iam-design
order: 4
description: Access at Wire Finance is built in layers. Every employee starts with a common foundation, then receives additional access based on where they work, what they do, and any separately approved requirements
---

We have organised Wire Finance into departments, roles, and security groups.

Now those groups need to mean something.

Being a member of `SG-DEPT-FINANCE-USERS` tells us that someone belongs to Finance.

Being a member of `SG-ROLE-SOC-ANALYST` tells us that someone performs the SOC Analyst role.

But neither group is useful until we define **what access that relationship should actually provide**.

This is where Wire Finance introduces **access baselines**.

Instead of designing permissions separately for every employee, normal access is built in layers.

```text
Effective Access
=
Company Baseline
+ Department Baseline
+ Role Baseline
+ Approved Exceptions
```

Privileged administrative access is deliberately kept outside this formula and authorised separately.

![Wire Finance Access Entitlement Flow]({{ '/assets/images/active-directory/access_entitlement_flow.png' | relative_url }})

### What Is an Access Baseline?

An access baseline defines the access that should normally exist because of a known business relationship.

For example:

- every employee needs certain company-wide services
- Finance employees need Finance resources
- a Finance Analyst needs resources specific to financial analysis
- some employees may require additional access that falls outside their normal role

Each layer answers a different question.

```text
What does every employee need?
        ↓
Company Baseline

Where does the employee work?
        ↓
Department Baseline

What does the employee do?
        ↓
Role Baseline

Do they need something outside the normal model?
        ↓
Approved Exception
```

The objective is not to give users as much access as possible.

It is to make **normal access predictable, explainable, and reviewable**.

> These baselines describe the intended Wire Finance access model. Individual services, applications, policies, and permissions are implemented and validated separately as the environment is built.

### Company-Wide Baseline

The first layer applies to the normal Wire Finance workforce.

Wire Finance defines this as:

`WF-BASE-001 – Standard Employee Baseline`

The group representing this relationship is:

`SG-BASE-ALL-STAFF`

Regardless of whether someone works in Finance, HR, Operations, Sales, or IT, every standard employee begins with the same foundation.

| Access Area | Standard Employee Baseline |
|---|---|
| **Identity** | Named Microsoft Entra user account |
| **Account Type** | Standard user |
| **Authentication** | MFA required as part of the security baseline |
| **Productivity** | Standard Microsoft 365 productivity services |
| **Collaboration** | Approved company-wide collaboration resources |
| **General Company Resources** | Employee-wide resources only |
| **Department Resources** | Added through department baseline |
| **Role-Specific Resources** | Added through role baseline |
| **Sensitive or Exceptional Access** | Requires separate approval |
| **Privileged Access** | None by default |
| **Local Administrator Rights** | None by default |

The purpose of `WF-BASE-001` is to give an employee what they need simply because they work for Wire Finance.

It deliberately makes no assumption about their department, job function, or administrative responsibilities.

```text
Employee
   │
   ▼
SG-BASE-ALL-STAFF
   │
   ▼
WF-BASE-001
Standard Employee Baseline
```

Olivia in Finance, Maya in HR, Noah in Sales, and Taylor in IT all begin here.

Being an employee does **not** automatically provide access to departmental data, sensitive systems, privileged services, or administrator rights.

Those relationships must be established separately.

### Department Access Baselines

The next layer answers:

**Where does the employee work?**

Wire Finance maintains a baseline for each department.

| Department | Group | Standard Department Access | Not Included Automatically |
|---|---|---|---|
| **Finance** | `SG-DEPT-FINANCE-USERS` | Finance collaboration, documents, and approved departmental resources | Payment authority, payroll administration, privileged access |
| **HR** | `SG-DEPT-HR-USERS` | HR collaboration, recruitment, and approved employee-information resources | Payroll administration, Finance resources, privileged access |
| **Operations** | `SG-DEPT-OPERATIONS-USERS` | Operational collaboration, workflows, and approved customer-processing resources | Sensitive financial systems, privileged access |
| **Sales** | `SG-DEPT-SALES-USERS` | Sales collaboration, customer/account resources, and approved sales tools | Finance administration, HR resources, privileged access |
| **IT** | `SG-DEPT-IT-USERS` | IT collaboration, technical documentation, and service-management resources | Administrative roles, security administration, privileged systems |

The important word here is **baseline**.

A department baseline provides the resources normally associated with working in that department.

It does not grant every capability that might exist within that business area.

#### Finance Example

Olivia Carter belongs to Finance.

```text
Olivia Carter
        │
        ▼
SG-BASE-ALL-STAFF
        │
        ▼
SG-DEPT-FINANCE-USERS
```

Her Finance membership provides the normal resources required to work within the department.

It does **not** automatically provide payment-processing authority.

That distinction matters because:

```text
Works in Finance
        ≠
Authorised to process payments
```

#### IT Example

The same principle becomes even more important in IT.

Membership of:

`SG-DEPT-IT-USERS`

means someone belongs to the IT department.

It does **not** automatically make that employee an administrator.

```text
Works in IT
        ≠
Has administrative privilege
```

Administrative authority remains separately controlled.

Across all departments, the principle is the same:

**department membership tells us where someone works, but not everything they are authorised to do.**

### Role Access Baselines

The third layer answers:

**What does the employee actually do?**

Two employees can belong to the same department and still require different access because their responsibilities are different.

Wire Finance therefore uses role groups alongside department groups.

| Role | Group | Role-Specific Access | Key Boundary |
|---|---|---|---|
| **Finance Analyst** | `SG-ROLE-FINANCE-ANALYST` | Approved financial-analysis resources and financial records within scope | Payment authority not included |
| **HR Manager** | `SG-ROLE-HR-MANAGER` | Employee and HR-management information within approved responsibility | Payroll processing requires separate approval |
| **Operations Specialist** | `SG-ROLE-OPERATIONS-SPECIALIST` | Operational and customer-processing resources within assigned responsibility | Sensitive financial systems excluded |
| **Sales Representative** | `SG-ROLE-SALES-REPRESENTATIVE` | Sales resources and customer information within assigned responsibility | Finance and HR administration excluded |
| **IT Administrator** | `SG-ROLE-IT-ADMIN` | Represents the IT administration job function | Administrative privilege is separately authorised |
| **SOC Analyst** | `SG-ROLE-SOC-ANALYST` | Represents security investigation and response responsibilities | Everyday account does not automatically become privileged |
| **Deployment / Support Engineer** | `SG-ROLE-ENDPOINT-ADMIN` | Represents endpoint deployment and support responsibilities | Endpoint administration is separately authorised |
| **Head of Security Operations** | `SG-ROLE-SECURITY-LEADERSHIP` | Represents security leadership responsibilities | Elevated security administration remains separately authorised |

A role group describes a person's **business responsibility**.

It does not necessarily grant administrative authority directly.

That distinction is particularly important for the IT team.

Taylor Reed may be a SOC Analyst.

Casey Ng may be responsible for endpoint deployment.

Alex Morgan may work as an IT Administrator.

Jordan Lee may lead Security Operations.

Their job functions explain **why** elevated capability may eventually be required.

They do not mean those capabilities should automatically exist on their everyday employee identities.

### Putting the Baselines Together

Take Olivia Carter.

Olivia is a Finance Analyst.

Her normal access can be understood as a series of relationships:

```text
Olivia Carter
      │
      ▼
Company Baseline
SG-BASE-ALL-STAFF
      │
      ▼
Finance Department Baseline
SG-DEPT-FINANCE-USERS
      │
      ▼
Finance Analyst Role Baseline
SG-ROLE-FINANCE-ANALYST
```

Each layer exists for a different reason.

The **company baseline** provides what Olivia needs because she is an employee.

The **Finance department baseline** adds what she normally needs because she works in Finance.

The **Finance Analyst role baseline** adds what she needs because of the job she performs.

This creates a predictable definition of normal access.

Instead of asking:

**What permissions should we manually give Olivia?**

we can ask:

```text
Is she an employee?
        ↓
Company baseline

Where does she work?
        ↓
Finance baseline

What does she do?
        ↓
Finance Analyst baseline
```

The access model becomes easier to understand because every entitlement has a reason.

### Exception Access

Not every legitimate access requirement will fit neatly into the normal company, department, and role baselines.

An employee may occasionally need:

- access to a sensitive resource
- additional information for a specific piece of work
- temporary access outside their normal responsibilities
- a business capability deliberately excluded from their baseline

Wire Finance treats this as **exception access**.

![Wire Finance Exception Access Flow]({{ '/assets/images/active-directory/access_exception_flow.png' | relative_url }})

Exception access does not mean bypassing the access model.

It means deliberately recording why additional access is required.

Wire Finance uses the naming convention:

`SG-EXCEPTION-*`

One example is:

`SG-EXCEPTION-PAYMENT-PROCESSING`

This exists because being a Finance employee or Finance Analyst does not automatically mean someone should be allowed to process payments.

```text
Finance Employee
        ↓
Finance Baseline

Finance Analyst
        ↓
Finance Analyst Baseline

Approved Payment Authority
        ↓
SG-EXCEPTION-PAYMENT-PROCESSING
```

The additional capability is visible as a separate decision rather than silently becoming part of the employee's normal role.

### Exception Access Requirements

Access outside the normal baseline should have enough information to explain why it exists.

| Requirement | Expected |
|---|---|
| **User requiring access** | Yes |
| **Requested resource or capability** | Yes |
| **Business justification** | Yes |
| **Manager approval** | Yes |
| **Resource or data owner approval** | Where applicable |
| **Security review** | When required |
| **Start date** | Yes |
| **Expiry or review date** | Yes |
| **Request or ticket reference** | Yes |
| **Access owner** | Yes |

This gives Wire Finance something the normal baselines cannot provide:

**accountability for access outside the expected model.**

For every exception, we should be able to answer:

- Who received the access?
- What did they receive?
- Why was it needed?
- Who approved it?
- Who owns the access decision?
- When should it be reviewed or removed?

That final question is particularly important.

Temporary access that has no expiry or review point can quietly become permanent access simply because nobody returns to remove it.

Exception access therefore remains possible, but it should never become invisible.

### The Wire Finance Access Formula

We can now express normal employee access using a simple model:

```text
Effective Access
=
Company Baseline
+ Department Baseline
+ Role Baseline
+ Approved Exceptions
```

Or visually:

```text
Employee
   │
   ▼
Company Baseline
   │
   ▼
Department Baseline
   │
   ▼
Role Baseline
   │
   ▼
Approved Exception
   only when justified
```

The first three layers define **normal access**.

The fourth layer handles legitimate requirements outside that normal model.

There is one category deliberately missing from this formula:

**privileged administrative access.**

Privileged access is different because it allows an identity to administer, configure, investigate, or otherwise affect the environment itself.

Wire Finance therefore does not treat privileged authority as simply another employee entitlement.

```text
Normal Employee Access
=
Company
+ Department
+ Role
+ Approved Exceptions

Privileged Access
=
Separately authorised
```

This preserves one of the most important principles in the Wire Finance IAM design:

**having a job that requires administration is not the same thing as carrying administrative privilege everywhere.**

### The Access Model Is Now Defined

We began with groups that described relationships.

Now those relationships have meaning.

Wire Finance can explain normal access by asking four questions:

```text
Does the person work for Wire Finance?
        ↓
Company Baseline

Where do they work?
        ↓
Department Baseline

What do they do?
        ↓
Role Baseline

Do they have an approved requirement outside the normal model?
        ↓
Exception Access
```

That gives us a structured answer to the question that has followed the IAM design from the beginning:

**Who should have access to what — and why?**

But administrative authority still needs stronger treatment.

We know that some Wire Finance employees will need to manage identities, endpoints, security services, or even recover the tenant when normal administration fails.

Those capabilities should not simply be added to the normal access model.

The next part of the IAM design therefore asks:

**How should Wire Finance control privileged and emergency access?**