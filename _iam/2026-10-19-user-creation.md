---
layout: lesson
title: "User Creation"
series: wire-finance
parent: entra-id-implementation
order: 4
description: "Create and validate Wire Finance's first eight Microsoft Entra workforce identities, including employee IDs, authoritative attributes, pilot group assignments and group-based Microsoft 365 E5 licensing."
---

Having a well-designed identity and group model is one thing. Giving real employees identities that follow it is another.

We had already defined our workforce attributes and group rules, but when we reached the first employee-device onboarding checkpoint, we found a missing prerequisite: **our pilot users had not yet been created**.

Rather than create only Olivia Carter, our first intended endpoint user, I decided to build **all eight standard pilot accounts**. That would let us test the same identity standard across IT, HR, Finance, Operations and Sales before proceeding.

This page records what I actually configured, including the small details I nearly missed.

### 1. Find the right place to create a user

I opened the [Microsoft Entra admin center](https://entra.microsoft.com/) and followed:

**Identity → Users → All users → New user → Create new user**

The wizard had four stages:

`Basics → Properties → Assignments → Review + create`

Under **Basics**, I supplied the user's sign-in name, display name and initial password settings.

| Field | Wire Finance setting |
|---|---|
| User principal name | `firstname.lastname@<your-tenant-domain>` |
| Display name | The employee's full name, e.g. `Jordan Lee` |
| Mail nickname | Derived from the sign-in name |
| Password | **Auto-generate password** selected |
| Account enabled | **Yes** — checked |

We used the tenant's existing `onmicrosoft.com` domain for the lab. The custom company domain is a **future target**, not something we had already configured.

![Initial Microsoft Entra user creation screen]({{ '/assets/images/entra-id-implementation/user-creation-basics-initial.png' | relative_url }})

*Figure: The initial Basics screen. This is an in-progress screenshot, not the completed account: I had entered `jordan.lee`, but the display name still needed to be corrected from `Jordan` to `Jordan Lee`.*

That was our first practical reminder to check **both** the login name and the display name before saving.

I also confirmed that **Account enabled** needed to remain checked for active employees. We would use disabled accounts only for a deliberate pre-staging, leaver or security scenario.

### 2. Populate the attributes our groups depend on

On the **Properties** tab, I entered the standard identity data.

These values weren't decorative: the dynamic group model depends on them being consistent.

For Olivia, the example was:

| Property | Value |
|---|---|
| First name / Last name | `Olivia` / `Carter` |
| Display name | `Olivia Carter` |
| Department | `Finance` |
| Job title | `Finance Analyst` |
| Company name | `Wire Finance` |
| Employee ID | `WF-EMP-006` |
| Employee type | `Employee` |
| User type | `Member` |
| Account enabled | `Yes` |

**Exact wording matters.**

`Finance Analyst` and `Financial Analyst` may sound interchangeable, but an exact-match dynamic membership rule does not treat them as the same value.

The same applies to `Wire Finance` versus `Wire Financials`.

#### The employee ID we added during implementation

I noticed the **Employee ID** field while filling in the employee properties.

We had a user naming standard, but not yet a permanent workforce identifier, so we adopted:

`WF-EMP-###`

This is a unique, permanent identifier. It must not be reused after an employee leaves, and it must not change when their name, department or job title changes.

| Employee ID | Employee | Department | Exact job title |
|---|---|---|---|
| `WF-EMP-001` | Jordan Lee | IT | Head of Security Operations |
| `WF-EMP-002` | Alex Morgan | IT | IT Administrator |
| `WF-EMP-003` | Taylor Reed | IT | SOC Analyst |
| `WF-EMP-004` | Casey Ng | IT | Deployment/Support Engineer |
| `WF-EMP-005` | Maya Patel | HR | HR Manager |
| `WF-EMP-006` | Olivia Carter | Finance | Finance Analyst |
| `WF-EMP-007` | Ethan Brooks | Operations | Operations Specialist |
| `WF-EMP-008` | Noah Williams | Sales | Sales Representative |

![Microsoft Entra employment identity fields]({{ '/assets/images/entra-id-implementation/user-creation-employment-fields.png' | relative_url }})

*Figure: The Properties area where Employee ID, Employee type, hire date, office location and manager are entered. The screenshot shows the fields before their values were populated.*

I set **Employee type = Employee** for all eight.

I deliberately left **hire date and manager** blank rather than fabricate HR records or reporting lines we hadn't approved.

The employee ID and type were added alongside the core company, department and role attributes.

### 3. Assign the pilot group, not administrative permissions

On **Assignments**, we used:

| Assignment field | Selection |
|---|---|
| Groups | `SG-PILOT-IDENTITY` |
| Microsoft Entra roles | **None** |
| Administrative unit | **None** |

`SG-PILOT-IDENTITY` is an **Assigned** security group.

It would not gain members automatically, so I selected it during the user-creation process for each of the eight employees.

I did **not** manually add users to `SG-BASE-ALL-STAFF`, `SG-DEPT-*` or `SG-ROLE-*`.

Those groups were designed to evaluate the user's trusted attributes dynamically. Manually trying to reproduce dynamic memberships would hide mistakes in our rules or attributes.

Also, **Jordan, Alex, Taylor and Casey received no Entra administrative role through their everyday employee accounts**.

Our separate `adm-*` privileged identities and PIM controls are later implementation work, not something to simulate by giving standard accounts elevated access.

After checking **Review + create**, I repeated the process until all eight accounts existed.

### 4. Fix a licensing prerequisite: Usage location

The next important detail appeared **after** account creation.

Before group-based licensing, we needed the employees' **Usage location**, not merely a postal country field.

I updated all eight accounts to use the same location values.

To reproduce this, go to **Microsoft Entra admin center → Identity → Users → All users** and edit the users' properties (in bulk where the portal offers **Edit properties**, or individually):

| Property | Selected value |
|---|---|
| Usage location | **United Kingdom** (`GB`) |
| Country or region | **United Kingdom** |

These are separate fields.

Usage location affects service licensing eligibility. Country or region is useful profile information, but **it is not a substitute for Usage location**.

We left unnecessary address fields blank.

### 5. Assign E5 through a dedicated security group

We could have assigned Microsoft 365 E5 individually eight times.

Instead, we created a group so that **licensing is separate from pilot participation**.

In **Entra admin center → Identity → Groups → All groups → New group**, I created:

| Setting | Value |
|---|---|
| Group type | `Security` |
| Group name | `SG-LICENCE-M365-E5` |
| Membership type | **Assigned** |
| Microsoft Entra roles can be assigned | **No** |
| Description | Assigns Microsoft 365 E5 licensing to approved Wire Finance standard workforce identities |
| Members | All eight standard pilot users |

Why **Assigned**, not Dynamic User?

We only had 25 E5 seats and wanted explicit control during the pilot.

Converting licensing membership to a carefully validated dynamic rule is a **future production-maturity decision**, not something we had implemented yet.

For the licensing assignment, the portal route we used was the [Microsoft 365 admin center](https://admin.microsoft.com/):

**Billing → Licenses → Microsoft 365 E5 → Groups → Assign licenses**

We assigned Microsoft 365 E5 to `SG-LICENCE-M365-E5`, leaving the E5 service plans enabled for this initial test.

The group now controlled the pilot users' licensing entitlement, while `SG-PILOT-IDENTITY` continued to represent their pilot participation.

For readers reproducing this step, Microsoft's [group-based licensing walkthrough](https://learn.microsoft.com/en-us/entra/identity/users/groups-dynamic-tutorial#assign-licenses) explains the product's **Groups** assignment tab.

After saving, inspect the licensed users and the product's **Errors & issues** area rather than assuming that selecting **Assign** means every account received a licence successfully.

### 6. Verify the result instead of trusting the creation wizard

For each new identity, I returned to **Entra → Identity → Users → All users → [employee]** and reviewed **Group memberships**, **Assigned licenses** and **Assigned roles**.

Olivia was our most useful test case.

Her identity record displayed **one assigned licence**, and our review showed all five intended Wire Finance memberships:

| Group | Membership method | Result |
|---|---|---|
| `SG-BASE-ALL-STAFF` | Dynamic | Present |
| `SG-DEPT-FINANCE-USERS` | Dynamic | Present |
| `SG-ROLE-FINANCE-ANALYST` | Dynamic | Present |
| `SG-PILOT-IDENTITY` | Assigned | Present |
| `SG-LICENCE-M365-E5` | Assigned | Present |

![Olivia identity and licence count]({{ '/assets/images/entra-id-implementation/olivia-identity-licence-overview.png' | relative_url }})

*Figure: Olivia's Entra account overview. It showed six group memberships and one assigned licence. The sixth membership deserved investigation rather than being ignored.*

![Olivia's actual group memberships]({{ '/assets/images/entra-id-implementation/olivia-group-membership-review.png' | relative_url }})

*Figure: Membership review showing the intended dynamic and assigned groups, alongside a separate Microsoft 365 collaboration group.*

#### Why did Olivia have six groups instead of five?

The extra membership was a **Microsoft 365 group named `Wire Finance`**, with its own group email address.

It was not one of our `SG-*` security groups.

During the wider review, **seven pilot employees had this extra group; Casey Ng had the five intended memberships only**.

We could identify the group's type and cloud-managed source, but **not who originally created it or why**.

A cloud source does not prove the group was created automatically with the tenant.

We therefore did **not** delete it, add Casey simply to equalise counts, or treat it as part of the approved security-group taxonomy.

Its ownership and associated collaboration resources remain an investigation item.

This gave us a useful validation rule: **check for unexpected access as well as missing access**.

If a dynamic membership is missing in your own lab, first verify the exact `companyName`, `department`, `jobTitle`, `userType` and `accountEnabled` values.

Then inspect the group's **Dynamic membership rules → Validate rules** feature.

Group evaluation can be asynchronous, so do not immediately overwrite working rules because the membership has not appeared yet.

See [Microsoft's rule-validation instructions](https://learn.microsoft.com/en-us/entra/identity/users/groups-dynamic-rule-validation).

### What we had achieved

We now had **eight enabled standard employee identities**, consistent workforce IDs and attributes, the intentional pilot membership, and Microsoft 365 E5 licensing managed separately through `SG-LICENCE-M365-E5`.

Olivia's automatic Finance memberships and the five intended security-group relationships were visible in our validation evidence.

We had **not** created separate privileged `adm-*` identities or implemented PIM.

We had also **not** enabled Self-Service Password Reset. Those remained separate tasks.

Most importantly, we avoided signing Olivia into another computer just to prepare her account.

We wanted the first employee sign-in and initial credential experience to be tested through the actual device onboarding journey, which we'll document in the endpoint implementation chapter.