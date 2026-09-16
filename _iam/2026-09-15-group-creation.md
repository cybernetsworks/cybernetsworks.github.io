---
layout: lesson
title: "Group Creation"
series: wire-finance
parent: entra-id-implementation
order: 3
description: "The access model has been designed. Now we turn company baselines, departments, roles, exceptions, pilots, and privileged-access paths into real Microsoft Entra security groups."
---

The Wire Finance tenant has been prepared and its starting state recorded.

Now the environment can begin to change.

Earlier in the project, we designed a group structure around the organisation.

We created categories for:

- Company-wide access
- Departments
- Job roles
- Pilot deployments
- Exception access
- Privileged access

At that point, those groups existed only as part of the IAM design.

Now we can turn that design into real Microsoft Entra security groups.

#### Why Build the Groups Before the Users?

It might seem more natural to create the employees first.

But Wire Finance already has an access model.

By building the group structure first, future identities have somewhere meaningful to belong as soon as they are created.

Instead of creating a user and then asking:

**What permissions should this person have?**

we can eventually ask:

**Which approved groups should this identity belong to?**

That gives us a much cleaner model.

```text
Identity
   ↓
Group Membership
   ↓
Entitlement
   ↓
Access
```

The group identifies **who should receive an entitlement**.

The role, application, policy, licence, or resource connected to that group determines **what the entitlement actually allows**.

That distinction becomes increasingly important as the environment grows.

#### From Group Design to Group Objects

The original IAM design contained a catalogue of groups Wire Finance expected to need.

The implementation inventory is different.

It records what actually exists inside Microsoft Entra ID.

At this checkpoint, **19 Microsoft Entra security groups** have been created.

They include:

```text
Company Baseline
      +
Department Groups
      +
Role Groups
      +
Pilot Groups
      +
Exception Groups
      +
Privileged Access Groups
```

But not every group uses the same membership model.

Some can be populated automatically from trusted identity attributes.

Others require an explicit decision or approval.

That difference is central to the Wire Finance implementation.

---

#### Automating Group Membership

One of the most important decisions during group creation was how users would become members.

Wire Finance could manage every group manually.

An administrator could create a user, open the Finance group, add the employee, open the Finance Analyst group, add them again, and repeat the process whenever somebody joins, moves, or changes role.

That would work.

But it would also introduce more manual administration and make it easier for group membership to become outdated.

Instead, where membership can be determined from trusted identity information, Wire Finance uses **Dynamic User** groups.

```text
User Attributes
      ↓
Dynamic Membership Rule
      ↓
Does the identity match?
     /        \
   Yes         No
    ↓           ↓
Member      Not a Member
```

Microsoft Entra evaluates the attributes of an identity against the membership rule.

If the identity satisfies the rule, it can become a member automatically.

If those attributes later change and the identity no longer satisfies the rule, the membership can also be updated automatically.

This gives Wire Finance an important layer of identity lifecycle automation.

#### Building Rules Around Trusted Attributes

The rules are deliberately more specific than checking only a department or job title.

For example, the Finance department group does not simply check:

```text
department = Finance
```

It also verifies that the identity:

- Belongs to Wire Finance
- Is a Member identity
- Has an enabled account

Conceptually:

```text
Correct Company
      +
Correct Department
      +
Member Identity
      +
Account Enabled
      ↓
Department Membership
```

Role groups add another condition:

```text
Correct Company
      +
Correct Department
      +
Correct Job Title
      +
Member Identity
      +
Account Enabled
      ↓
Role Membership
```

This prevents one matching attribute from being enough to place an identity into a group.

#### Authoritative Identity Attributes

Dynamic membership makes identity attributes part of the access-control model.

Wire Finance therefore defines authoritative values that should be used consistently.

| Identity / Attribute | Authoritative Value |
|---|---|
| **Standard workforce `companyName`** | Wire Finance |
| **Privileged identity `companyName`** | Wire Finance-Privileged |
| **Emergency identity `companyName`** | Wire Finance-Emergency |
| **Departments** | Finance; HR; Operations; Sales; IT |
| **Finance role title** | Finance Analyst |
| **HR role title** | HR Manager |
| **Operations role title** | Operations Specialist |
| **Sales role title** | Sales Representative |
| **IT administrator title** | IT Administrator |
| **SOC role title** | SOC Analyst |
| **Endpoint role title** | Deployment/Support Engineer |
| **Security leadership title** | Head of Security Operations |

These values may look like ordinary profile information.

But once an attribute participates in dynamic membership, changing it may affect access.

```text
Identity Attribute
        ↓
Dynamic Membership Rule
        ↓
Group Membership
        ↓
Entitlement
        ↓
Effective Access
```

That makes attribute accuracy security-sensitive.

#### Company-Wide Workforce

The broadest dynamic group is:

`SG-BASE-ALL-STAFF`

Membership type:

`Dynamic User`

Rule:

```text
(user.companyName -eq "Wire Finance") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

This represents the standard Wire Finance workforce.

It also connects directly to the company baseline defined earlier:

```text
WF-BASE-001
      ↓
SG-BASE-ALL-STAFF
```

The baseline describes what standard employees should receive.

The group gives us a population to which those entitlements can eventually be assigned.

The `companyName` check is particularly useful because privileged and emergency identities use different authoritative values:

```text
Standard Workforce    → Wire Finance
Privileged Identities → Wire Finance-Privileged
Emergency Identities  → Wire Finance-Emergency
```

Those identity types therefore do not automatically fall into the normal workforce population.

#### Department Groups

The next layer represents **where an employee works**.

Wire Finance has five departments, and each department has a Dynamic User security group.

##### Finance

Group:

`SG-DEPT-FINANCE-USERS`

```text
(user.companyName -eq "Wire Finance") -and
(user.department -eq "Finance") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

##### Human Resources

Group:

`SG-DEPT-HR-USERS`

```text
(user.companyName -eq "Wire Finance") -and
(user.department -eq "HR") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

##### Operations

Group:

`SG-DEPT-OPERATIONS-USERS`

```text
(user.companyName -eq "Wire Finance") -and
(user.department -eq "Operations") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

##### Sales

Group:

`SG-DEPT-SALES-USERS`

```text
(user.companyName -eq "Wire Finance") -and
(user.department -eq "Sales") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

##### IT

Group:

`SG-DEPT-IT-USERS`

```text
(user.companyName -eq "Wire Finance") -and
(user.department -eq "IT") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

The department attribute can therefore drive department membership automatically.

```text
Employee
   ↓
department
   ↓
Dynamic Rule
   ↓
Department Group
   ↓
Department Entitlements
```

This is the technical implementation of the department baselines designed earlier.

#### Role Groups

Department membership tells us **where someone works**.

It does not necessarily tell us **what they do**.

That is why Wire Finance also uses role-specific Dynamic User groups.

##### Finance Analyst

Group:

`SG-ROLE-FINANCE-ANALYST`

```text
(user.companyName -eq "Wire Finance") -and
(user.department -eq "Finance") -and
(user.jobTitle -eq "Finance Analyst") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

##### HR Manager

Group:

`SG-ROLE-HR-MANAGER`

```text
(user.companyName -eq "Wire Finance") -and
(user.department -eq "HR") -and
(user.jobTitle -eq "HR Manager") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

##### Operations Specialist

Group:

`SG-ROLE-OPERATIONS-SPECIALIST`

```text
(user.companyName -eq "Wire Finance") -and
(user.department -eq "Operations") -and
(user.jobTitle -eq "Operations Specialist") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

##### Sales Representative

Group:

`SG-ROLE-SALES-REPRESENTATIVE`

```text
(user.companyName -eq "Wire Finance") -and
(user.department -eq "Sales") -and
(user.jobTitle -eq "Sales Representative") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

##### IT Administrator

Group:

`SG-ROLE-IT-ADMIN`

```text
(user.companyName -eq "Wire Finance") -and
(user.department -eq "IT") -and
(user.jobTitle -eq "IT Administrator") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

##### SOC Analyst

Group:

`SG-ROLE-SOC-ANALYST`

```text
(user.companyName -eq "Wire Finance") -and
(user.department -eq "IT") -and
(user.jobTitle -eq "SOC Analyst") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

##### Deployment / Support Engineer

Group:

`SG-ROLE-ENDPOINT-ADMIN`

```text
(user.companyName -eq "Wire Finance") -and
(user.department -eq "IT") -and
(user.jobTitle -eq "Deployment/Support Engineer") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

##### Head of Security Operations

Group:

`SG-ROLE-SECURITY-LEADERSHIP`

```text
(user.companyName -eq "Wire Finance") -and
(user.department -eq "IT") -and
(user.jobTitle -eq "Head of Security Operations") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

The role groups give us the third layer of the access model.

For example:

```text
Finance Analyst
      ↓
SG-BASE-ALL-STAFF
      +
SG-DEPT-FINANCE-USERS
      +
SG-ROLE-FINANCE-ANALYST
```

The same model applies throughout the organisation.

#### What This Means for a Real Identity

Consider Taylor Reed.

Taylor is the Wire Finance SOC Analyst.

The identity is expected to have attributes such as:

```text
companyName    = Wire Finance
department     = IT
jobTitle       = SOC Analyst
userType       = Member
accountEnabled = true
```

Those attributes satisfy several dynamic rules.

```text
Taylor Reed
    │
    ├── companyName = Wire Finance
    │        ↓
    │   SG-BASE-ALL-STAFF
    │
    ├── department = IT
    │        ↓
    │   SG-DEPT-IT-USERS
    │
    └── jobTitle = SOC Analyst
             ↓
       SG-ROLE-SOC-ANALYST
```

Instead of an administrator manually maintaining three separate memberships, the identity attributes can drive the expected membership automatically.

That gives us a technical implementation of the access model designed earlier:

```text
Company Baseline
      +
Department Baseline
      +
Role Baseline
```

#### Connecting Dynamic Membership to the Mover Process

This automation becomes particularly useful when somebody changes role or department.

Imagine Taylor moves from:

```text
SOC Analyst
```

to:

```text
IT Administrator
```

If the authoritative `jobTitle` value changes, Microsoft Entra can re-evaluate the relevant dynamic rules.

Conceptually:

```text
jobTitle = SOC Analyst
        ↓
SG-ROLE-SOC-ANALYST

        CHANGES TO

jobTitle = IT Administrator
        ↓
SG-ROLE-IT-ADMIN
```

The same principle applies to department changes.

```text
department = Sales
      ↓
SG-DEPT-SALES-USERS

      CHANGES TO

department = Operations
      ↓
SG-DEPT-OPERATIONS-USERS
```

The old membership does not need to remain simply because somebody forgot to remove it manually.

This does not eliminate the Mover review process.

The Mover process still determines **what should change**.

Dynamic membership gives Microsoft Entra a mechanism to automate part of that change.

---

#### Not Every Group Should Be Dynamic

Automation makes sense when membership can be determined from authoritative identity attributes.

But not every access decision works that way.

Some memberships exist because somebody made an explicit decision or granted an approval.

Those groups remain **Assigned**.

The implementation therefore follows a simple rule:

```text
Can membership be reliably determined
from authoritative attributes?
            │
        Yes │ No
            │
        ┌───┴───┐
        ↓       ↓
     Dynamic  Assigned
       User
```

This distinction prevents automation from replacing decisions that should still require human approval.

#### Pilot Group

The initial identity pilot group is:

`SG-PILOT-IDENTITY`

Membership type:

`Assigned`

Participation in a pilot is deliberate.

A user should not automatically become part of a security or deployment pilot simply because of their department or job title.

```text
Selected for Pilot
      ↓
Assigned Membership
      ↓
SG-PILOT-IDENTITY
```

This gives Wire Finance a controlled population for testing future identity and security changes before wider deployment.

#### Exception Access

Wire Finance also created:

`SG-EXCEPTION-PAYMENT-PROCESSING`

Membership type:

`Assigned`

This matches the exception-access model designed earlier.

Sensitive payment-processing access should not appear automatically because someone happens to have a particular job title.

Instead:

```text
Business Requirement
        ↓
Approval
        ↓
Assigned Membership
        ↓
SG-EXCEPTION-PAYMENT-PROCESSING
```

The access exists because there is a justified exception, not because a profile attribute matched a rule.

#### Privileged Access Groups

The final category is privileged access.

Three privileged-access groups have been created:

| Privileged Access Group | Membership | Entra Role Assignable |
|---|---|---|
| `PAG-ENTRA-SECURITY-READER` | Assigned | Yes |
| `PAG-DEFENDER-SECURITY-OPERATOR` | Assigned | No |
| `PAG-INTUNE-ENDPOINT-ADMIN` | Assigned | No |

Privileged membership is deliberately **Assigned**.

Administrative access should not suddenly appear just because somebody's job title changes.

The naming convention tells us these groups are intended to sit in front of privileged capabilities:

```text
PAG-*
```

But there is another important distinction.

**Creating the privileged-access group does not create the privilege itself.**

For example:

`PAG-INTUNE-ENDPOINT-ADMIN`

identifies who should eventually receive the endpoint-administration entitlement.

It does not define what an Endpoint Administrator can actually do.

The group answers:

**Who receives the entitlement?**

The platform authorization system answers:

**What does that entitlement permit?**

That distinction becomes important in the next stage of the implementation.

#### Current Group Inventory

At this checkpoint, the Wire Finance Microsoft Entra group inventory contains 19 security groups:

| Group | Membership | Status |
|---|---|---|
| `SG-BASE-ALL-STAFF` | Dynamic User | Created |
| `SG-DEPT-FINANCE-USERS` | Dynamic User | Created |
| `SG-DEPT-HR-USERS` | Dynamic User | Created |
| `SG-DEPT-OPERATIONS-USERS` | Dynamic User | Created |
| `SG-DEPT-SALES-USERS` | Dynamic User | Created |
| `SG-DEPT-IT-USERS` | Dynamic User | Created |
| `SG-ROLE-FINANCE-ANALYST` | Dynamic User | Created |
| `SG-ROLE-HR-MANAGER` | Dynamic User | Created |
| `SG-ROLE-OPERATIONS-SPECIALIST` | Dynamic User | Created |
| `SG-ROLE-SALES-REPRESENTATIVE` | Dynamic User | Created |
| `SG-ROLE-IT-ADMIN` | Dynamic User | Created |
| `SG-ROLE-SOC-ANALYST` | Dynamic User | Created |
| `SG-ROLE-ENDPOINT-ADMIN` | Dynamic User | Created |
| `SG-ROLE-SECURITY-LEADERSHIP` | Dynamic User | Created |
| `SG-PILOT-IDENTITY` | Assigned | Created |
| `SG-EXCEPTION-PAYMENT-PROCESSING` | Assigned | Created |
| `PAG-ENTRA-SECURITY-READER` | Assigned | Created |
| `PAG-DEFENDER-SECURITY-OPERATOR` | Assigned | Created |
| `PAG-INTUNE-ENDPOINT-ADMIN` | Assigned | Created |

That gives us:

```text
14 Dynamic User Groups
         +
5 Assigned Groups
         =
19 Security Groups
```

More importantly, the membership model now reflects the kind of decision each group represents.

```text
Company / Department / Role
            ↓
    Attribute Driven
            ↓
       Dynamic User


Pilot / Exception / Privileged
            ↓
   Decision or Approval Driven
            ↓
          Assigned
```

#### Automation Still Needs Validation

Dynamic membership reduces manual work.

It does not eliminate the need to verify the logic.

A badly written rule can automate the wrong decision just as efficiently as a correct rule automates the right one.

Before these groups are relied on for access, the expected identities should be tested against the rules.

For each pilot user, we should be able to predict:

```text
Which company group should they enter?

Which department group should they enter?

Which role group should they enter?

Which groups should they NOT enter?
```

When the users are created, the resulting memberships can then be compared against those expectations.

That gives us a practical test of both the identity attributes and the dynamic rules.

#### The Access Structure Now Exists

The tenant now contains the group architecture needed to support the Wire Finance access model.

We have built:

```text
Company Baseline Groups
        +
Department Groups
        +
Role Groups
        +
Pilot Groups
        +
Exception Groups
        +
Privileged Access Groups
```

Some memberships will be driven automatically by trusted identity information.

Others remain deliberately controlled through explicit assignment.

But the privileged-access groups reveal the next problem.

A group such as:

`PAG-INTUNE-ENDPOINT-ADMIN`

can tell us **who should receive endpoint-administration access**.

It still does not tell Microsoft Intune what that administrator should actually be allowed to do.

And:

`PAG-DEFENDER-SECURITY-OPERATOR`

does not yet define what actions a Security Operator can perform in Microsoft Defender.

The membership structure now exists.

The next challenge is **authorization**.

**Where should those administrative permissions be defined, and how do we make sure each privileged group receives only the permissions its role actually requires?**