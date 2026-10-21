---
layout: lesson
title: "Group Creation"
series: wire-finance
parent: entra-id-implementation
order: 4
description: "The access model has been designed. Now we turn company baselines, departments, roles, exceptions, pilots, and privileged-access paths into real Microsoft Entra security groups."
---

We now have our first eight Wire Finance employees in Microsoft Entra ID.

Their identities have been created, their organisational attributes are established, and we know which departments and roles they represent.

But creating users is only part of the identity implementation.

We also need a structured way to determine which identities belong to which access populations.

Earlier, we designed an access model built around company-wide, department, role, pilot, exception, and privileged-access groups.

Now we can examine how that design was implemented in Microsoft Entra ID.

**The goal is to make group membership reflect business responsibility rather than depend on administrators remembering every assignment.**

### From Group Design to Group Objects

The original IAM design established the groups Wire Finance would need.

Implementation gives us something different: actual security groups with membership types, rules, and purposes.

The first group-creation checkpoint contained 19 Microsoft Entra security groups.

As the implementation progressed, I added a dedicated Microsoft 365 E5 licensing group and five device groups to support the endpoint provisioning model.

The current documented inventory is:

| Group Category | Number | Membership Model |
|---|---:|---|
| Company baseline | 1 | Dynamic User |
| Department | 5 | Dynamic User |
| Role | 8 | Dynamic User |
| Identity pilot | 1 | Assigned |
| Exception access | 1 | Assigned |
| Licensing | 1 | Assigned |
| Privileged access | 3 | Assigned |
| Device | 5 | Dynamic Device / Assigned |
| **Total** | **25** | |

This represents the current implemented security-group inventory, not simply a list of groups we might create in the future.

But there is an important distinction.

**Not every group should determine its membership in the same way.**

Some memberships can be derived from trusted information about the identity.

Others exist because somebody has deliberately selected or authorised membership.

### Choosing the Right Membership Model

When creating the Microsoft Entra security groups, the membership model depended on the purpose of each group.

For normal company, department, and job-role membership, we use **Dynamic User** groups.

For pilot participation, licensing during the pilot, exceptions, and privileged access, we use **Assigned** groups.

The decision looks like this:

```text
What determines group membership?
              |
       +------+------+
       |             |
 Trusted User    Explicit Decision
 Attributes      or Approval
       |             |
       v             v
  Dynamic User     Assigned
       |             |
       +------+------+
              |
       Group Membership
```

This distinction is important.

We want routine organisational membership to be as consistent and automated as possible.

But we do not want sensitive access, administrative privilege, or pilot participation to appear automatically just because an employee has a particular job title.

### Building Dynamic Membership Rules

A Dynamic User group evaluates identity attributes against a membership rule.

When the attributes satisfy the rule, Microsoft Entra can add the identity to the group automatically.

When those attributes change, Entra can re-evaluate membership.

This is useful for the employee lifecycle we designed earlier.

But the rules need to be specific enough to avoid unintended membership.

#### Company-Wide Workforce

Our company baseline group is:

`SG-BASE-ALL-STAFF`

Membership type: `Dynamic User`

The implemented rule is:

```text
(user.companyName -eq "Wire Finance") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

This selects enabled Member identities belonging to the standard Wire Finance workforce.

Notice that the rule does not simply check whether an account exists.

It also checks the company attribute, user type, and account state.

This matters because our privileged and emergency identities use different company values:

| Identity Type | companyName |
|---|---|
| Standard workforce | `Wire Finance` |
| Privileged administrator | `Wire Finance-Privileged` |
| Emergency access | `Wire Finance-Emergency` |

This helps keep administrative and emergency identities outside the normal workforce baseline.

#### Department Membership

Next, we created groups representing the five Wire Finance departments.

Take Finance as an example:

`SG-DEPT-FINANCE-USERS`

Membership type: `Dynamic User`

```text
(user.companyName -eq "Wire Finance") -and
(user.department -eq "Finance") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

The same pattern is used for HR, Operations, Sales, and IT, with the appropriate department value.

This means department membership is derived from the employee's organisational information rather than maintained entirely through manual assignments.

#### Role Membership

Department membership tells us where an employee works.

It does not necessarily tell us what responsibilities they perform.

That is why we also created role-specific groups.

For example:

`SG-ROLE-FINANCE-ANALYST`

Membership type: `Dynamic User`

```text
(user.companyName -eq "Wire Finance") -and
(user.department -eq "Finance") -and
(user.jobTitle -eq "Finance Analyst") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

Here, the rule evaluates both department and job title, alongside the standard workforce conditions.

An identity must satisfy the full rule to qualify.

This pattern is applied across the eight implemented role groups.

### The Workforce Group Inventory

Instead of repeating nearly identical membership rules for every group, the tables below show the implemented groups and the attributes that distinguish their memberships.

All of these groups use the common workforce conditions established above.

#### Company and Department Groups

| Group | Membership | Additional Matching Attribute |
|---|---|---|
| `SG-BASE-ALL-STAFF` | Dynamic User | Company baseline |
| `SG-DEPT-FINANCE-USERS` | Dynamic User | Finance |
| `SG-DEPT-HR-USERS` | Dynamic User | HR |
| `SG-DEPT-OPERATIONS-USERS` | Dynamic User | Operations |
| `SG-DEPT-SALES-USERS` | Dynamic User | Sales |
| `SG-DEPT-IT-USERS` | Dynamic User | IT |

#### Role Groups

| Group | Department | Matching Job Title |
|---|---|---|
| `SG-ROLE-FINANCE-ANALYST` | Finance | Finance Analyst |
| `SG-ROLE-HR-MANAGER` | HR | HR Manager |
| `SG-ROLE-OPERATIONS-SPECIALIST` | Operations | Operations Specialist |
| `SG-ROLE-SALES-REPRESENTATIVE` | Sales | Sales Representative |
| `SG-ROLE-IT-ADMIN` | IT | IT Administrator |
| `SG-ROLE-SOC-ANALYST` | IT | SOC Analyst |
| `SG-ROLE-ENDPOINT-ADMIN` | IT | Deployment/Support Engineer |
| `SG-ROLE-SECURITY-LEADERSHIP` | IT | Head of Security Operations |

These 14 groups form our attribute-driven workforce structure.

However, a role group such as `SG-ROLE-IT-ADMIN` does not automatically grant Microsoft Entra administrator privileges.

It represents the employee's business role.

Administrative authorisation remains a separate decision.

### Validating Dynamic Membership

After implementing the groups and creating the pilot identities, I verified that the dynamic membership rules behaved as expected.

Olivia Carter provided a useful example.

Her identity was configured with:

```text
Employee:      Olivia Carter
Employee ID:   WF-EMP-006
Company:       Wire Finance
Department:    Finance
Job Title:     Finance Analyst
User Type:     Member
Account:       Enabled
```

Based on those attributes, Microsoft Entra evaluated her into:

```text
Olivia Carter
       |
       +-- SG-BASE-ALL-STAFF
       |
       +-- SG-DEPT-FINANCE-USERS
       |
       +-- SG-ROLE-FINANCE-ANALYST
```

These memberships were confirmed during validation.

The same department and role membership patterns were observed across the other pilot employees.

This demonstrates something important.

**The group rules are not just documentation. They are operating against the identities we created.**

It also shows why employee attributes need to be governed carefully.

Changing a security-sensitive attribute could change group membership and potentially affect access to resources assigned to those groups.

Dynamic membership helps automate part of the Joiner, Mover, and Leaver processes, but it does not replace proper business approval or access review.

### Groups That Require Explicit Assignment

Not every membership should be automated.

Some groups represent decisions that cannot safely be inferred from department or job title.

For Wire Finance, six user/access groups use Assigned membership.

| Group | Membership | Purpose |
|---|---|---|
| `SG-PILOT-IDENTITY` | Assigned | Identity and deployment pilot population |
| `SG-EXCEPTION-PAYMENT-PROCESSING` | Assigned | Approved payment-processing exceptions |
| `SG-LICENCE-M365-E5` | Assigned | Microsoft 365 E5 licensing |
| `PAG-ENTRA-SECURITY-READER` | Assigned | Entra Security Reader access path |
| `PAG-DEFENDER-SECURITY-OPERATOR` | Assigned | Defender security operations access path |
| `PAG-INTUNE-ENDPOINT-ADMIN` | Assigned | Intune endpoint administration access path |

Each group has a specific purpose.

#### Pilot Membership

`SG-PILOT-IDENTITY` identifies employees included in the initial identity and endpoint implementation pilot.

The eight workforce accounts were deliberately assigned to this group.

Pilot participation should be controlled because the group can be used to target configurations and deployment changes before wider rollout.

#### Exception Access

`SG-EXCEPTION-PAYMENT-PROCESSING` represents a controlled exception to normal role-based access.

Membership is Assigned because payment-processing authority should not automatically follow from a Finance department or job title.

The group provides a place to apply an approved entitlement.

It does not itself prove that a payment-processing permission has already been granted to a user.

#### Group-Based E5 Licensing

Another important implementation decision was introducing:

`SG-LICENCE-M365-E5`

This is an Assigned security group used to control Microsoft 365 E5 licensing during the pilot.

All eight initial workforce identities were added to the group.

The Microsoft 365 E5 licence was assigned through group-based licensing, and Olivia Carter's inherited licence assignment was verified.

This gives us two separate decisions:

```text
SG-PILOT-IDENTITY
       |
       +-- Who participates in the pilot?

SG-LICENCE-M365-E5
       |
       +-- Who receives an E5 licence?
```

Although the same eight users currently belong to both, the groups are not interchangeable.

Pilot membership controls participation.

Licensing membership controls licence assignment.

Keeping them separate allows the licensing model to evolve without making every future employee dependent on membership of a testing group.

### Privileged Access Groups

The three `PAG-*` groups were created to support the privileged-access architecture defined during IAM design.

One important configuration difference is that:

`PAG-ENTRA-SECURITY-READER`

was created as an Entra role-assignable security group.

The Defender and Intune privileged groups are regular Assigned security groups because their authorisation is managed through their respective platforms.

However, creating a privileged-access group is not the same as implementing the permissions behind it.

For example:

```text
PAG-INTUNE-ENDPOINT-ADMIN
          |
          v
Intune Role Assignment
          |
          v
WF-Intune-Endpoint-Admin
          |
          v
Approved Endpoint Permissions
```

The custom `WF-Intune-Endpoint-Admin` role and its pilot assignment have already been implemented in Microsoft Intune.

The Defender Unified RBAC role and the Entra Security Reader group-to-role assignment remain separate follow-on activities.

We will explore these authorisation boundaries in the next implementation page.

### Device Groups Added to the Inventory

As the endpoint implementation progressed, five additional security groups were introduced for device management and provisioning.

| Device Group | Membership |
|---|---|
| `SG-DEVICES-WINDOWS-CORPORATE` | Dynamic Device |
| `SG-DEVICES-WINDOWS-PILOT` | Assigned |
| `SG-DEVICES-PRIVILEGED` | Dynamic Device |
| `SG-DEVICES-NONCOMPLIANT-TEST` | Assigned |
| `SG-DEVICES-AUTOPILOT-CORPORATE` | Dynamic Device |

These groups support different aspects of our device model.

For example, `SG-DEVICES-AUTOPILOT-CORPORATE` uses Autopilot classification to identify devices intended for the standard corporate provisioning path.

`SG-DEVICES-PRIVILEGED` uses the PAW classification.

Pilot and noncompliance-test membership remain explicitly assigned for controlled testing.

The detailed device classification rules and design decisions belong to our earlier Device Provisioning Model.

For this chapter, the important distinction is that **device classification, device management, and device trust are not the same thing**.

A device entering a group does not automatically prove that it is enrolled, compliant, or secure.

### An Unexpected Group During Validation

One useful discovery came from reviewing the pilot users' actual group memberships.

Alongside our deliberately created security groups, a separate Microsoft 365 collaboration group named:

`Wire Finance`

was present in the tenant.

Seven of the eight pilot users appeared as members.

Casey Ng was the exception.

This group was cloud-managed and had an associated tenant email address.

However, its original creation path and purpose had not been confirmed.

Rather than deleting or modifying an unfamiliar group, I left it unchanged for further investigation.

This is an important reminder that not every group found in Microsoft Entra belongs to our formal security-group architecture.

The additional collaboration group is not included in the 25 security groups documented here.

**An unexpected object should be understood before it is changed.**

### The Implemented Group Architecture

At the latest documented checkpoint, Wire Finance has:

```text
14 Dynamic User Groups
         +
 6 Assigned User/Access Groups
         +
 3 Dynamic Device Groups
         +
 2 Assigned Device Groups
         |