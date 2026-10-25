---
layout: lesson
title: "Intune Administrative Access and RBAC"
series: wire-finance
parent: endpoint-device-management-implementation
order: 1
description: "Create and verify Wire Finance's custom Microsoft Intune endpoint-administrator role, document its limited permission set, and scope the pilot assignment before enrolling the first Windows device."
---

Our eight pilot users and their security groups were now documented. We had also established that **a person's job title is not an administrative permission**.

Before building our first Windows endpoint, I wanted to answer a different question: **who should be able to manage it in Intune—and how much authority should they receive?**

I could have performed all endpoint work with a highly privileged tenant account. That would have been easier in the short term, but it would teach us very little about operating a company safely. Instead, we created a limited **Microsoft Intune RBAC** role and assigned it to our existing endpoint-administration group.

> **Implementation note:** In the lab, I configured this role before creating our eight standard employee accounts. The privileged-access group and Windows pilot device group already existed. The website introduces workforce identities first for a clearer learning sequence, but the administrative-role configuration below is the work we actually carried out.

### 1. Understand the four parts of Intune access

Our design separates the role from the people receiving it and the objects they can manage:

```text
WF-Intune-Endpoint-Admin          WHAT actions are permitted
             +
PAG-INTUNE-ENDPOINT-ADMIN         WHO can receive those actions
             +
SG-DEVICES-WINDOWS-PILOT          WHICH devices are in the pilot scope
             +
Default scope tag                 WHICH tagged Intune objects are visible
```

These objects are not interchangeable. In particular, `PAG-INTUNE-ENDPOINT-ADMIN` is an **Entra security group**, while `WF-Intune-Endpoint-Admin` is a **custom Intune role**, not an Entra custom directory role.

We had created both security groups in our earlier group-design work. We would use a sufficiently authorized setup account to create the role; dedicated `adm-*` identities and Privileged Identity Management were **still to come**.

### 2. Create the custom Intune role

I opened the [Microsoft Intune admin center](https://intune.microsoft.com/) and followed the navigation that appeared in our tenant:

**Tenant administration → Roles → All roles → Create → Intune role**

Under **Basics**, I entered:

| Setting | Value |
|---|---|
| Name | `WF-Intune-Endpoint-Admin` |
| Description | Least-privilege endpoint administration for approved Wire Finance administrators; initially scoped to the Windows pilot population. |
| Role type | Custom **Intune role** |

The description communicates the role's purpose; the actual control comes from the permission selections, not from its name.

#### Permissions: our v1 selection

On **Permissions**, I expanded the relevant categories and selected the capabilities recorded in our implementation notes:

| Permission category | Recorded allowed actions |
|---|---|
| **Managed devices** | Read, Update, Set primary user, View reports |
| **Device configurations** | Read, Create, Update, Assign, View reports |
| **Device compliance policies** | Read, Create, Update, Assign, View reports |
| **Enrollment programs** | Read device, Read profile, Create profile, Update profile, Assign profile, Sync device |
| **Mobile apps** | Read, Create, Update, Assign |
| **Security baselines** | Read, Create, Update, Assign |
| **Endpoint Detection and Response** | Read, Create, Update, Assign, View reports |
| **Endpoint protection reports** | Read |
| **Microsoft Defender ATP** | Read |
| **Organization** | Read |
| **Remote tasks** | Sync devices, Reboot now, Collect diagnostics, Windows Defender |

This allowed pilot administrators to work with configurations, deployments, reports, onboarding profiles and selected support actions without handing them every possible Intune capability.

**What we deliberately did not grant:** device **Wipe**, **Retire** and **Delete** actions; retrieval of LAPS or other sensitive credentials; destructive Autopilot device deletion; or permission to manage Intune RBAC roles and role assignments. The intention was also to leave other unrelated and destructive permissions off.

There is a useful distinction here: **Managed devices → Update** does *not* automatically permit remote tasks such as wipe or reboot. Those are separate permissions. Likewise, **Enrollment programs → Sync device** is distinct from **Remote tasks → Sync devices**.

> **Evidence limit:** The original record preserves the v1 capability selections, but we did not retain a complete screenshot/export of every enabled and disabled permission toggle. This table is our implementation record, **not** a claim that we have independently rechecked the current saved role against every line. To verify a recreated role—or audit our live role—open **All roles → WF-Intune-Endpoint-Admin → Properties → Permissions** and inspect each category. Where your portal shows additional actions, leave them disabled unless you have justified them separately.

Microsoft maintains an up-to-date [Intune custom-role permission reference](https://learn.microsoft.com/en-us/intune/fundamentals/role-based-access-control/create-custom-role) explaining exactly what each action permits.

On **Scope (Tags)**, I selected **`Default`**, reviewed the configuration, and created the role. No custom scope tag was introduced for this pilot.

### 3. Assign the role to the Windows pilot

Creating a role does not, by itself, give anyone its permissions. The next step was an assignment connecting our custom role to an admin group and a manageable population.

I returned to:

**Tenant administration → Roles → All roles → WF-Intune-Endpoint-Admin → Assignments → Assign**

The assignment wizard used **Basics → Admin Groups → Scope (Groups) → Scope tags → Review + create**.

| Wizard stage | What I entered |
|---|---|
| **Basics** | Name: `WF-ASSIGN-INTUNE-ENDPOINT-ADMIN-PILOT` |
| **Description** | Assign the custom endpoint-administrator role to approved Wire Finance administrators, initially scoped to Windows pilot devices. |
| **Admin Groups** | Include `PAG-INTUNE-ENDPOINT-ADMIN` |
| **Scope (Groups)** | Include `SG-DEVICES-WINDOWS-PILOT` |
| **Scope tags** | `Default` |
| **Review + create** | Review selections, then create the assignment |

![Created Intune RBAC pilot assignment]({{ '/assets/images/endpoint-management/intune-rbac-assignment-created.png' | relative_url }})

There are two easy mistakes to avoid. First, **Admin Groups** identifies administrators; it must not be confused with **Scope (Groups)**, which identifies the users/devices the administrators can manage. Second, this assignment does **not** automatically place new endpoints into `SG-DEVICES-WINDOWS-PILOT`. That is an **Assigned** device group, so pilot-device membership must be managed deliberately when those devices exist.

Also, `SG-DEVICES-WINDOWS-PILOT` serves a different purpose from the dynamic `SG-DEVICES-AUTOPILOT-CORPORATE` group we'll use for initial provisioning. One controls a delegated management scope; the other will identify the appropriate Autopilot provisioning population.

### 4. Reopen and verify the saved assignment

After creation, I reopened **`WF-ASSIGN-INTUNE-ENDPOINT-ADMIN-PILOT`** rather than assuming the wizard had saved everything correctly.

The original verification showed:

- **Admin group:** `PAG-INTUNE-ENDPOINT-ADMIN` — **Active**
- **Scope (Groups):** `SG-DEVICES-WINDOWS-PILOT` — **Active**
- **Scope tags:** `Default`

![Verified Intune RBAC pilot assignment groups and scope tag]({{ '/assets/images/endpoint-management/intune-rbac-pilot-assignment-verified.png' | relative_url }})

This verifies **the configuration of the assignment**. It does **not** yet prove a named administrator can perform every approved task or is blocked from every prohibited one. We hadn't created and tested a dedicated privileged `adm-*` identity for this role.

That distinction matters because Intune permissions may be cumulative across different assignments. A user with a broader Microsoft Entra administrator role or another Intune assignment could have more power than this custom role alone suggests. Similarly, **`Default` is a visibility tag, not a complete security boundary**.

Microsoft's [Intune RBAC assignment guidance](https://learn.microsoft.com/en-us/intune/fundamentals/role-based-access-control/assign-role) and [scope-tag documentation](https://learn.microsoft.com/en-us/intune/fundamentals/role-based-access-control/scope-tags) explain these limits.

### 5. Our checkpoint before enrollment

We had now created:

```text
Custom Intune role
  WF-Intune-Endpoint-Admin
           ↓
Pilot role assignment
  WF-ASSIGN-INTUNE-ENDPOINT-ADMIN-PILOT
           ↓
Admin group              Scope group
  PAG-INTUNE-...           SG-DEVICES-WINDOWS-PILOT
           ↓
Scope tag: Default
```

**Confirmed:** the role was created; the assignment was saved; the admin group, pilot device scope and scope tag were reopened and verified.

**Still outstanding:** an action-by-action permission recheck against the live role, a practical allowed-versus-denied test using a dedicated privileged identity, and later security governance such as PIM. The separate `WF-Defender-Security-Operator` role had **not** been created in Defender Unified RBAC.

With our administrative foundation in place, the next prerequisite is to configure **automatic Intune MDM enrollment and Microsoft Entra device-join permissions**. Only after those foundations are in place will we build the first local Windows 11 VM and test its Autopilot journey.