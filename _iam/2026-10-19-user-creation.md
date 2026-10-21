---
layout: lesson
title: "User Creation"
series: wire-finance
parent: entra-id-implementation
order: 3
description: "Bringing the first eight Wire Finance employees into Microsoft Entra ID and establishing the identity attributes that will drive access."
---

We have established our Microsoft environment.

We have reviewed the tenant and confirmed that the capabilities we need are available.

Now it is time to introduce the people who will use it.

Earlier in our IAM design, we defined a 25-person workforce, established naming conventions, and decided how identities should be organised.

But those employees still exist only in our design.

**Now we need to turn them into real Microsoft Entra identities.**

And this involves more than creating usernames and passwords.

The information we assign to each employee will eventually help determine which groups they belong to, what resources they can access, and how their identity is managed.

### Starting with Eight Employees

Rather than creating all 25 employees immediately, I selected eight users for the initial implementation.

These represent different departments and responsibilities across Wire Finance.

| Employee ID | Employee | Department | Job Title |
|---|---|---|---|
| WF-EMP-001 | Jordan Lee | IT | Head of Security Operations |
| WF-EMP-002 | Alex Morgan | IT | IT Administrator |
| WF-EMP-003 | Taylor Reed | IT | SOC Analyst |
| WF-EMP-004 | Casey Ng | IT | Deployment/Support Engineer |
| WF-EMP-005 | Maya Patel | HR | HR Manager |
| WF-EMP-006 | Olivia Carter | Finance | Finance Analyst |
| WF-EMP-007 | Ethan Brooks | Operations | Operations Specialist |
| WF-EMP-008 | Noah Williams | Sales | Sales Representative |

This gives us a useful starting population.

For example, Olivia will help us validate Finance department and role membership.

Taylor represents our security operations function.

And Casey represents the IT responsibilities associated with endpoint deployment.

By starting with known employees and responsibilities, we can test whether our implementation behaves as the design intended.

### Creating Users in Microsoft Entra ID

I created the eight standard user accounts through the Microsoft Entra admin center.

The navigation used was:

```text
Microsoft Entra Admin Center
       ↓
Identity
       ↓
Users
       ↓
All users
       ↓
New user
       ↓
Create new user
```

For each employee, I established their account identity and organisational information.

Our IAM naming convention uses:

```text
firstname.lastname@wirefinance.com
```

However, the current lab still uses the Microsoft-provided `onmicrosoft.com` tenant domain.

For example, Olivia's identity follows the same naming structure:

```text
olivia.carter@<tenant>.onmicrosoft.com
```

Here, `<tenant>` represents the actual Microsoft tenant prefix.

The custom `wirefinance.com` domain remains part of our target design, not the current lab configuration.

The important point is that our identity naming remains consistent even though the domain suffix differs.

Each employee was created as a **standard workforce identity**, not an administrative account.

### Establishing the Right Identity Attributes

Creating the account is only the first part.

Next, we need to make sure Microsoft Entra understands who the employee is within Wire Finance.

For the initial eight users, I configured the following identity attributes:

| Attribute | Configured Value |
|---|---|
| Company name | `Wire Finance` |
| Employee ID | `WF-EMP-###` |
| Employee type | `Employee` |
| Department | Employee's assigned department |
| Job title | Employee's assigned role title |
| User type | `Member` |
| Account enabled | `Yes` |
| Usage location | United Kingdom |
| Country/region | United Kingdom |
| Administrative role | None assigned to standard identity |

The employee IDs were assigned using our permanent numbering standard, from `WF-EMP-001` through `WF-EMP-008`.

These identifiers remain unique to each employee and are not intended to change when someone moves departments or changes responsibilities.

Some fields, including hire date, office location, and manager, were intentionally left blank where authoritative employee information had not yet been established.

We should not invent HR information simply to populate every available field.

### Why These Attributes Matter

Consider Olivia Carter.

Her account includes:

```text
Employee:     Olivia Carter
Employee ID:  WF-EMP-006
Company:      Wire Finance
Department:   Finance
Job Title:    Finance Analyst
User Type:    Member
```

To a casual observer, these may look like ordinary employee details.

But in our access model, several of these values are security-sensitive.

For example, we designed dynamic groups that evaluate employee attributes.

That means Olivia's department and job title can influence her group membership automatically.

```text
Olivia Carter
      ↓
Company: Wire Finance
      ↓
Department: Finance
      ↓
Job Title: Finance Analyst
      ↓
Dynamic Group Evaluation
      ↓
Company + Department + Role Membership
```

This is why consistency matters.

If an employee's department or job title is entered incorrectly, the account may not receive the expected group membership.

And if those attributes change without proper authorisation, access could change as a result.

**Identity attributes are not always just descriptive information. Some of them become inputs into access decisions.**

We'll see this in action when we examine our group implementation.

### Pilot Membership and Licensing

The initial workforce was also placed into our controlled pilot population.

Each of the eight employees was explicitly assigned to:

`SG-PILOT-IDENTITY`

This group identifies the employees participating in the initial implementation and testing activities.

Unlike our department and role groups, pilot membership is deliberately assigned.

Being a Finance Analyst does not automatically make Olivia a pilot participant.

That requires a separate decision.

Microsoft 365 E5 licensing was also implemented through a dedicated assigned security group:

`SG-LICENCE-M365-E5`

All eight pilot employees were added to this licensing group, and group-based licence assignment was validated.

These two groups serve different purposes:

| Group | Purpose |
|---|---|
| `SG-PILOT-IDENTITY` | Controls participation in the pilot |
| `SG-LICENCE-M365-E5` | Controls assignment of Microsoft 365 E5 licences |

Keeping these decisions separate prevents pilot participation from becoming the permanent basis for workforce licensing.

The pilot licensing model is deliberately controlled while we validate the environment.

### Authentication at Account Creation

The new users were provisioned using temporary passwords, with password changes required at their first interactive sign-in.

The temporary passwords themselves are not recorded in this documentation.

This gives each employee an initial authentication path without treating the provisioning password as a permanent credential.

For Olivia, the first organisational sign-in would later become part of our Windows Autopilot deployment test.

Rather than signing her into another workstation beforehand, we preserved that first-sign-in experience for the endpoint provisioning journey.

We'll cover the authentication decisions and security controls in their dedicated implementation pages.

### Validating the First Eight Identities

After creating the accounts, I reviewed their configuration and group membership.

The implementation confirmed that:

- All eight standard workforce