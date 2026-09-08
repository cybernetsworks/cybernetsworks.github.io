---
layout: lesson
title: "Access Baselines"
series: wire-finance
parent: iam-design
order: 5
description: Access at Wire Finance is built in layers. Every employee starts with a common foundation, then receives additional access based on where they work and what they actually do.
---

We have organised Wire Finance into departments, roles, and security groups.

Now those groups need to mean something.

Being placed into `SG-DEPT-FINANCE-USERS` tells us that someone belongs to Finance.

Being placed into `SG-ROLE-SOC-ANALYST` tells us that someone performs the SOC Analyst role.

But neither group is useful until we define **what access membership should actually provide**.

This is where Wire Finance introduces **access baselines**.

Rather than building permissions separately for every employee, access is constructed in layers:

![Wire Finance Access Entitlement Flow]({{ '/assets/images/active-directory/access_entitlement_flow.png' | relative_url }})

---
#### Company-Wide Baseline
The first layer applies to everyone.

Wire Finance defines this as:

`WF-BASE-001 – Standard Employee Baseline`

Regardless of whether someone works in Finance, HR, Operations, Sales, or IT, every employee begins with the same basic foundation.

| Access Area | Standard Employee Baseline |
|---|---|
| **Identity** | Named Microsoft Entra user account |
| **Account Type** | Standard User |
| **Authentication** | MFA required |
| **Productivity** | Outlook, Teams, Office |
| **Collaboration** | Company-wide SharePoint and intranet resources |
| **General Company Resources** | Employee-wide resources only |
| **Department Resources** | Granted through department baseline |
| **Role-Specific Resources** | Granted through role baseline |
| **Additional or Sensitive Access** | Requires separate approval |
| **Privileged Access** | None by default |
| **Local Administrator Rights** | None by default |

The purpose of `WF-BASE-001` is to give every employee enough access to function as part of Wire Finance without assuming anything about their department or job role.

An employee receives a named identity, standard productivity tools, company-wide collaboration resources, and MFA as part of that common starting point.

But the baseline also establishes clear boundaries.

Being an employee does **not** automatically provide access to departmental resources, sensitive information, privileged systems, or local administrator rights.

Those permissions must come from another layer of the access model.

So whether the employee is Olivia in Finance, Maya in HR, Noah in Sales, or Taylor in IT, they all begin here:

```text
Employee
   │
   ▼
WF-BASE-001
Standard Employee Baseline
```
---
#### Department Access Baseline

The company-wide baseline gives every Wire Finance employee the same starting point.

The next layer adds access based on **where the employee works**.

Wire Finance defines a separate baseline for Finance, HR, Operations, Sales, and IT so that employees receive the resources normally required by their department without automatically inheriting access to everything else.

##### Finance Department Baseline

**Group:** `SG-DEPT-FINANCE-USERS`

| Access Area | Finance Baseline |
|---|---|
| **Company baseline** | Included |
| **Finance Teams workspace** | Member access |
| **Finance SharePoint** | Standard departmental access |
| **Finance documents** | Standard departmental resources |
| **Finance applications** | Approved general Finance applications |
| **Finance data** | Access limited to department-appropriate information |
| **Payment processing** | Not included by default |
| **Payroll information** | Not included by default |
| **HR resources** | No access |
| **IT administrative resources** | No access |

The Finance baseline provides the resources normally required to work within the department, but it deliberately stops before more sensitive activities are introduced.

Being a member of Finance does not automatically provide payment-processing authority or access to payroll information.

Those requirements need to be justified separately.

##### Human Resources Department Baseline

**Group:** `SG-DEPT-HR-USERS`

| Access Area | HR Baseline |
|---|---|
| **Company baseline** | Included |
| **HR Teams workspace** | Member access |
| **HR SharePoint** | Standard departmental access |
| **Recruitment resources** | Standard HR access |
| **Employment information** | Access according to job responsibility |
| **Payroll administration** | Not included by default |
| **Finance resources** | No access |
| **IT administrative resources** | No access |

HR employees need access to information that would be inappropriate for most of the organisation, particularly employee and recruitment information.

But even within HR, access still has limits.

Payroll administration is not included simply because someone belongs to the HR department.

##### Operations Department Baseline

**Group:** `SG-DEPT-OPERATIONS-USERS`

| Access Area | Operations Baseline |
|---|---|
| **Company baseline** | Included |
| **Operations Teams workspace** | Member access |
| **Operations SharePoint** | Standard departmental access |
| **Operational workflow resource** | Standard access |
| **Customer-processing resources** | Access according to operational responsibility |
| **Finance resources** | No access |
| **HR resources** | No access |
| **IT administrative resources** | No access |

Operations employees receive the collaboration and workflow resources needed to support day-to-day business activity.

Where customer-processing information is involved, access is determined by the employee's operational responsibility rather than department membership alone.

##### Sales Department Baseline

**Group:** `SG-DEPT-SALES-USERS`

| Access Area | Sales Baseline |
|---|---|
| **Company baseline** | Included |
| **Sales Teams workspace** | Member access |
| **Sales SharePoint** | Standard departmental access |
| **Sales applications** | Standard sales tools |
| **Customer and account information** | Access according to sales responsibility |
| **Finance administration** | No access |
| **HR resources** | No access |
| **IT administrative resources** | No access |

Sales employees receive the tools and customer information required to perform their responsibilities.

But access to customer and account information is still linked to what the employee is responsible for rather than treating the entire department as one unrestricted access boundary.

##### IT Department Baseline

**Group:** `SG-DEPT-IT-USERS`

| Access Area | IT Department Baseline |
|---|---|
| **Company baseline** | Included |
| **IT Teams workspace** | Member access |
| **IT SharePoint** | Standard departmental access |
| **Technical documentation** | Standard IT access |
| **Service-management resources** | Standard access |
| **Administrative privileges** | Not granted through department membership |
| **Security administration** | Not granted through department membership |
| **Privileged systems** | Requires separate privileged access assignment |

The IT baseline makes one of the most important boundaries in the design explicit:

**Working in IT does not automatically make someone an administrator.**

Membership of `SG-DEPT-IT-USERS` provides access to the resources needed to operate as a member of the IT department.

Administrative privileges, security administration, and access to privileged systems remain separate.

That keeps departmental membership from becoming a shortcut to elevated access.

Across all five departments, the same pattern appears.

Department membership provides **standard departmental access**, but it does not automatically provide every permission associated with that area of the business.

Finance does not automatically receive payment authority.

HR does not automatically receive payroll administration.

Operations and Sales only receive information appropriate to their responsibilities.

IT membership does not automatically provide administrative privileges.

A department therefore tells us **where someone works**.

It still does not completely tell us **what they do**.

Two employees can belong to the same department and require very different access because their job functions are different.

That brings us to the third layer of the access model:

---
#### Role Access Baselines

A department tells us **where someone works**.

A role tells us **what they actually do**.

Wire Finance therefore uses role access baselines to provide additional entitlements based on an employee's specific job function.

These permissions build on the company-wide and department baselines rather than replacing them.

```text
Employee
   │
   ▼
Company-Wide Baseline
   │
   ▼
Department Baseline
   │
   ▼
Role Access Baseline
```

##### Finance Analyst

**Role ID:** `WF-ROLE-FIN-ANL-001`  
**Group:** `SG-ROLE-FINANCE-ANALYST`

| Field | Baseline |
|---|---|
| **Department** | Finance |
| **Company baseline** | Required |
| **Department baseline** | Finance |
| **Default Applications** | Outlook, Teams, Office |
| **Finance resources** | Standard Finance resources |
| **Role-specific resources** | Finance analyst resources |
| **Sensitive data** | Financial records within approved scope |
| **Payment authority** | Not included |
| **Privileged access** | None |
| **Access owner** | Finance manager |
| **Additional approval** | Required for sensitive financial systems |

The Finance Analyst role adds the resources needed for financial analysis while keeping more sensitive capabilities outside the normal baseline.

Access to financial records is limited to the approved scope, payment authority is not included, and sensitive financial systems require additional approval.

##### HR Manager

**Role ID:** `WF-ROLE-HR-MGR-001`  
**Group:** `SG-ROLE-HR-MANAGER`

| Field | Baseline |
|---|---|
| **Department** | Human Resources |
| **Company baseline** | Required |
| **Department baseline** | HR |
| **Default Applications** | Outlook, Teams, Office |
| **HR resources** | Standard HR resources |
| **Employee records** | Access within approved responsibility |
| **Payroll processing** | Separate approval required |
| **Privileged access** | None |
| **Access owner** | HR manager |

The HR Manager role provides access to employee records within approved responsibilities.

But even here, the role has boundaries.

Payroll processing is not automatically included simply because someone manages HR resources. It requires separate approval.

##### Operations Specialist

**Role ID:** `WF-ROLE-OPS-SPEC-001`  
**Group:** `SG-ROLE-OPERATIONS-SPECIALIST`

| Field | Baseline |
|---|---|
| **Department** | Operations |
| **Company baseline** | Required |
| **Department baseline** | Operations |
| **Default Applications** | Outlook, Teams, Office |
| **Operations resources** | Standard operational resources |
| **Customer-processing systems** | Access within assigned responsibilities |
| **Sensitive financial systems** | No access by default |
| **Privileged access** | None |
| **Access owner** | Operations manager |

The Operations Specialist receives access to the operational resources needed for the role, including customer-processing systems within assigned responsibilities.

Sensitive financial systems remain outside the baseline.

##### Sales Representative

**Role ID:** `WF-ROLE-SALES-REP-001`  
**Group:** `SG-ROLE-SALES-REPRESENTATIVE`

| Field | Baseline |
|---|---|
| **Department** | Sales |
| **Company baseline** | Required |
| **Department baseline** | Sales |
| **Default Applications** | Outlook, Teams, Office |
| **Sales resources** | Standard Sales resources |
| **Customer information** | Access within assigned accounts |
| **Finance administration** | No access |
| **HR resources** | No access |
| **Privileged access** | None |
| **Access owner** | Sales manager |

The Sales Representative role provides access to standard Sales resources and customer information associated with assigned accounts.

It does not extend into Finance administration, HR resources, or privileged access.

---

##### IT Administrator

**Group:** `SG-ROLE-IT-ADMIN`

The IT Administrator role works slightly differently.

The employee's standard account receives the normal company-wide baseline together with standard IT departmental resources.

Administrative permissions are **not** added to that normal productivity account.

Instead, administrative activity requires a separate privileged identity.

```text
IT Administrator
      │
      ├── Standard Account
      │      └── Company + IT departmental access
      │
      └── Privileged Identity
             └── Approved IT administration
```

This allows the employee to perform normal day-to-day work without carrying administrative privileges everywhere they go.

##### SOC Analyst

**Group:** `SG-ROLE-SOC-ANALYST`

The SOC Analyst's standard account provides access to normal company and IT collaboration resources.

Security investigation and response permissions are assigned separately to the analyst's approved privileged identity.

```text
SOC Analyst
    │
    ├── Standard Account
    │      └── Company + IT collaboration
    │
    └── Privileged Identity
           └── Security investigation
               and response permissions
```

The role describes Taylor's security responsibilities, but those responsibilities do not turn the everyday account into a privileged account.

##### Deployment / Support Engineer

**Group:** `SG-ROLE-ENDPOINT-ADMIN`

The Deployment / Support Engineer also begins with normal employee and IT departmental access.

Endpoint-management or deployment privileges are assigned separately through the employee's privileged administrative identity.

```text
Deployment / Support Engineer
            │
            ├── Standard Account
            │      └── Company + IT departmental access
            │
            └── Privileged Identity
                   └── Endpoint and deployment
                       administration
```

Again, the job may require elevated capability, but the normal account remains separate from the identity used to perform privileged tasks.

##### Head of Security Operations

**Group:** `SG-ROLE-SECURITY-LEADERSHIP`

The Head of Security Operations uses a standard account for normal business and security collaboration.

Elevated security administration is performed only through a separate privileged account and approved administrative role assignments.

```text
Head of Security Operations
           │
           ├── Standard Account
           │      └── Business + security collaboration
           │
           └── Privileged Identity
                  └── Approved elevated
                      security administration
```

This preserves the same separation we established earlier in the IAM design:

**normal work happens through a normal identity, while privileged activity happens through a deliberately separate privileged identity.**

---

#### Putting the Baselines Together

We can now see how normal access is built for an employee.

Take Olivia Carter, our Finance Analyst.

```text
Olivia Carter
      │
      ▼
WF-BASE-001
Company-Wide Baseline
      │
      ▼
Finance Department Baseline
      │
      ▼
Finance Analyst Role Baseline
```

Each layer adds access for a different reason.

The **company-wide baseline** provides what Olivia needs simply because she is an employee.

The **Finance department baseline** adds the resources normally required by someone working in Finance.

The **Finance Analyst role baseline** adds the resources required to perform her specific job.

The same pattern applies throughout Wire Finance:

```text
Employee
   │
   ├── What does every employee need?
   │      └── Company-Wide Baseline
   │
   ├── Where does the employee work?
   │      └── Department Baseline
   │
   └── What job does the employee perform?
          └── Role Access Baseline
```

The result is a predictable definition of **normal access**.

Instead of deciding permissions from scratch for every employee, Wire Finance now has a structured way to understand what access should normally exist and why it exists.

But not every legitimate access requirement will fit neatly into one of these three baselines.

An employee may occasionally need access to a sensitive resource, application, or dataset that falls outside their normal company, department, or role entitlements.

And when access falls outside the baseline, it should not simply be added and forgotten.

That raises our next question:

**How should Wire Finance handle exceptional or sensitive access that sits outside the normal access model?**

#### Wire Finance Exception Access

The company, department, and role baselines define what **normal access** should look like.

But legitimate access requirements will not always fit neatly inside those predefined boundaries.

An employee may need temporary access to a sensitive resource, additional information for a specific piece of work, or access that falls outside what their normal role would usually provide.

Wire Finance treats this as **exception access**.

![Wire Finance IAM Design]({{ '/assets/images/active-directory/access_exception_flow.png' | relative_url }})

The goal is not to prevent legitimate access.

It is to make sure that access outside the normal baseline has a clear reason, appropriate approval, an identified owner, and a point at which it should be reviewed.

##### Exception Access Requirements

**Exception Group Naming:** `SG-EXCEPTION-*`

| Requirement | Required |
|---|---|
| **User requesting access** | Yes |
| **Requested resources** | Yes |
| **Business justification** | Yes |
| **Manager approval** | Yes |
| **Application or data owner approval** | Yes |
| **Security review** | When required |
| **Start date** | Yes |
| **Expiry or review date** | Yes |
| **Ticket or request reference** | Yes |
| **Access owner** | Yes |

This gives Wire Finance something the normal baselines cannot provide:

**accountability for access that sits outside the expected model.**

If someone receives exceptional access, we should be able to answer:

- Who requested it?
- What resource was requested?
- Why was it required?
- Who approved it?
- Who owns the access?
- When should it be reviewed or removed?

That last question matters.

Without an expiry or review point, temporary access has a habit of becoming permanent access simply because nobody returns to remove it.

Exception access therefore remains possible — but it should never become invisible.

---

#### Wire Finance Privileged Access Principles

Exception access deals with permissions outside the normal baseline.

Privileged access goes a step further.

It gives an identity the ability to administer, configure, investigate, or otherwise affect the environment itself.

Throughout the Wire Finance IAM design, we have deliberately kept those capabilities separate from normal productivity accounts.

![Wire Finance IAM Design]({{ '/assets/images/active-directory/privileged_access_flow.png' | relative_url }})

An IT Administrator may need to manage the tenant.

A SOC Analyst may need investigation and response permissions.

A Deployment / Support Engineer may need endpoint and Intune administration.

The Head of Security Operations may require controlled high-level security administration.

But those responsibilities do not mean their everyday accounts should carry those privileges.

##### Privileged Role Separation

**Privileged Access Group Convention:** `PAG-*`

Example:

`PAG-ENTRA-SECURITY-READER`

| Role | Expected Privileged Area |
|---|---|
| **Head of Security Operations** | Security leadership and controlled high-level security administration |
| **IT Administrator** | Tenant and approved IT administration |
| **SOC Analyst** | Security investigation and response |
| **Deployment / Support Engineer** | Endpoint and Intune administration |

The principle is straightforward:

**normal work happens through a normal identity, while privileged activity happens through a deliberately separate privileged identity.**

This gives Wire Finance a much clearer separation between being an employee and acting with administrative authority.

---

#### The Access Model Is Now Defined

We started with security groups that told us where people belonged and what roles they performed.

Now those groups have meaning.

Wire Finance can build access through a predictable series of layers:

```text
Employee
   │
   ▼
Company-Wide Baseline
   │
   ▼
Department Baseline
   │
   ▼
Role Baseline
   │
   ▼
Exception / Sensitive Access
   │
   ▼
Privileged Access
   where separately required
```

The first three layers define **normal access**.

Exception access handles justified requirements outside that normal model.

Privileged access remains deliberately separated for activities that require elevated authority.

We now have a way to answer the question that has followed us throughout the IAM design:

**Who should have access to what — and why?**

But there is one problem left.

Access cannot remain static.

People join Wire Finance.

They change roles.

They move between departments.

Their responsibilities change.

And eventually, they leave.

A well-designed access model only works if access changes when the person does.

So the next challenge becomes:

**How should Wire Finance manage an identity throughout its entire lifecycle?**

**["Identity Lifecycle"](https://learning.cybernetswork.com/_iam/identity-lifecycle)**

