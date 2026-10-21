---
layout: lesson
title: "Administrative Roles"
series: wire-finance
parent: entra-id-implementation
order: 5
description: "Implementing least-privilege administrative access for Wire Finance, starting with Microsoft Intune RBAC and separating permissions across Entra, Intune, and Defender."
---

We now have our first eight employees in Microsoft Entra ID.

Their organisational attributes are established, and our group architecture is in place.

But there is an important distinction we haven't implemented fully yet.

**Being responsible for a system does not automatically mean having permission to administer it.**

Earlier, we created groups representing IT administrators, SOC analysts, endpoint administrators, and security leadership.

Those groups identify business responsibilities.

They do not automatically grant administrative privileges in Microsoft Entra, Intune, or Defender.

So how do we connect the right administrators to the right permissions without giving everyone more access than they need?

That is our next challenge.

### Understanding Where Administrative Permissions Belong

Wire Finance uses several Microsoft platforms, each responsible for different administrative functions.

For example:

- **Microsoft Entra ID** manages directory identities and directory roles.
- **Microsoft Intune** controls endpoint management through its own role-based access control system.
- **Microsoft Defender XDR** provides security administration capabilities through its supported RBAC model.

This distinction matters because administrative permissions are not all configured in one place.

An Intune custom role is not the same thing as a Microsoft Entra directory role.

And creating an Entra security group does not automatically create permissions in Microsoft Defender.

Our implementation therefore separates two responsibilities:

```text
Microsoft Entra Security Group
             |
             v
Who should receive access?
             |
             v
Platform Role / Permission
             |
             v
What can they actually do?
```

For Wire Finance, we created three privileged-access groups to support this model.

| Privileged Access Group | Intended Platform |
|---|---|
| `PAG-ENTRA-SECURITY-READER` | Microsoft Entra ID |
| `PAG-DEFENDER-SECURITY-OPERATOR` | Microsoft Defender XDR |
| `PAG-INTUNE-ENDPOINT-ADMIN` | Microsoft Intune |

Each group represents a different administrative responsibility.

However, the groups are only one part of the access model.

We still need to connect them to the appropriate platform permissions.

### Our First Implementation: Intune Endpoint Administration

The first custom administrative role I implemented was for Microsoft Intune.

This was a practical starting point because our next technical objective was to begin deploying and managing corporate Windows endpoints.

Wire Finance needs someone who can manage device configurations, applications, compliance policies, and routine endpoint support.

But that person should not automatically receive unrestricted administrative control over the entire Microsoft tenant.

So instead of relying on Global Administrator for routine endpoint operations, I created a dedicated Intune role:

`WF-Intune-Endpoint-Admin`

This is a **custom Microsoft Intune RBAC role**, not a custom Microsoft Entra directory role.

#### Creating the Intune Role

I created the role through the Microsoft Intune admin center.

The navigation used was:

```text
Microsoft Intune Admin Center
             ↓
Tenant administration
             ↓
Roles
             ↓
All roles
             ↓
Create
             ↓
Intune role
```

The role was named:

`WF-Intune-Endpoint-Admin`

Its purpose is to provide the permissions required for approved endpoint-management activities while withholding unnecessary administrative capabilities.

This is where our earlier least-privilege design begins becoming an actual platform configuration.

### Deciding What the Role Can Do

Creating a custom role means choosing which administrative actions should be available.

We don't want to assign every permission simply because an administrator might need it someday.

Instead, we start with the responsibilities the role is expected to perform.

The documented initial permission set covers activities such as:

| Area | Intended Administrative Capability |
|---|---|
| Managed devices | Read and update managed device information |
| Device configuration | Create, update, assign, and review configuration policies |
| Device compliance | Manage and review compliance policies |
| Applications | Create, update, and assign applications |
| Enrollment | Manage supported enrollment profiles and settings |
| Security baselines | Configure and assign endpoint security baselines |
| Endpoint detection and response | Manage supported EDR configurations and reports |
| Remote support | Perform selected actions such as device sync, reboot, and diagnostic collection |

These permissions support the everyday operational responsibilities of the endpoint-management function.

But just as important is what the role does **not** include.

#### Permissions Deliberately Excluded

The initial role does not include unrestricted access to destructive or highly sensitive operations.

The excluded capabilities include:

- Device wipe
- Device retirement
- Device deletion
- Sensitive credential and LAPS access
- Intune RBAC administration
- Destructive Windows Autopilot deletion

Why exclude these?

Because managing an endpoint does not automatically justify the ability to destroy its management record, erase its contents, or retrieve sensitive credentials.

Some of those operations may eventually be necessary.

But they should be introduced through an approved operational requirement rather than granted by default.

**Least privilege is not just about what an administrator can do. It is also about what they are deliberately prevented from doing.**

#### Connecting the Role to a Privileged Group

Creating `WF-Intune-Endpoint-Admin` defined the administrative permissions.

But we still needed to determine who would receive them.

Earlier, we created:

`PAG-INTUNE-ENDPOINT-ADMIN`

This is an Assigned Microsoft Entra security group intended for authorised endpoint administrators.

Rather than assigning the Intune role directly to individual accounts, we use the privileged-access group as the administrative assignment boundary.

The model becomes:

```text
Approved Administrator
         |
         v
PAG-INTUNE-ENDPOINT-ADMIN
         |
         v
Intune Role Assignment
         |
         v
WF-Intune-Endpoint-Admin
         |
         v
Permitted Endpoint Operations
```

This separation makes the arrangement easier to manage and review.

The role defines permissions.

The group identifies the intended administrators.

The assignment connects the two.

### Creating the Pilot Role Assignment

In Intune, I created the following role assignment:

`WF-ASSIGN-INTUNE-ENDPOINT-ADMIN-PILOT`

The assignment was deliberately scoped to our pilot environment.

Here is the implemented configuration:

| Assignment Component | Configured Value |
|---|---|
| Assignment name | `WF-ASSIGN-INTUNE-ENDPOINT-ADMIN-PILOT` |
| Intune role | `WF-Intune-Endpoint-Admin` |
| Admin group | `PAG-INTUNE-ENDPOINT-ADMIN` |
| Scope group | `SG-DEVICES-WINDOWS-PILOT` |
| Scope tag | `Default` |

There are four different concepts here, and understanding them is essential.

#### Who, What, Which, and Where

```text
WHO?
PAG-INTUNE-ENDPOINT-ADMIN
          |
          v
WHAT?
WF-Intune-Endpoint-Admin
          |
          v
WHICH DEVICES?
SG-DEVICES-WINDOWS-PILOT
          |
          v
WHERE / OBJECT VISIBILITY?
Default Scope Tag
```

The **admin group** determines the identities targeted by the administrative role assignment.

The **Intune role** defines the actions those administrators are permitted to perform.

The **scope group** defines the device or user population within the assignment's management scope. In our pilot, this is `SG-DEVICES-WINDOWS-PILOT`.

The **scope tag** contributes to the visibility boundary for Intune objects. Our initial assignment uses `Default`.

Together, these settings allow us to define administrative access more precisely than simply assigning broad tenant-wide permissions.

### Validating the Assignment

After creating the role assignment, I reopened it in the Intune admin center.

I verified that the configured administrative group, scope group, and scope tag matched the intended pilot design.

The assignment was successfully created and its configuration confirmed.

This established an important implementation milestone:

**The Intune administrative role and its pilot assignment now exist.**

However, verifying a role assignment's configuration is not the same as completing end-to-end privileged-user testing.

Our dedicated `adm-*` administrator identities have not yet been implemented.

That means the final validation of an approved privileged identity using this role remains a follow-on task.

We have established the authorisation structure, but we should not confuse that with a fully operational privileged-access lifecycle.

### What About Entra and Defender?

Intune is only one part of our administrative-access model.

The remaining privileged groups need to be connected to the correct authorisation systems.

#### Microsoft Entra Security Reader

For Entra, we created:

`PAG-ENTRA-SECURITY-READER`

This group is configured as an Entra role-assignable security group.

The intended authorisation is the built-in **Security Reader** directory role.

Unlike the custom Intune role, Security Reader already exists as a Microsoft Entra role.

We do not need to create a new custom directory role to reproduce its permissions.

However, the group-to-role assignment still needs to be verified or completed.

Creating a role-assignable group does not mean that the built-in role has already been assigned.

#### Microsoft Defender Security Operator

For Microsoft Defender, we created:

`PAG-DEFENDER-SECURITY-OPERATOR`

The planned custom authorisation role is:

`WF-Defender-Security-Operator`

This role belongs in the Microsoft Defender XDR Unified RBAC model.

It should not be created as a Microsoft Entra custom directory role simply because its members are represented by an Entra security group.

At our current implementation checkpoint, the Defender custom role has not yet been created.

It remains part of the later security implementation work.

### Administrative Access: Current Implementation Status

The distinction between created groups and operational permissions is worth recording.

| Area | Current Implementation Status |
|---|---|
| `PAG-ENTRA-SECURITY-READER` | Created; Entra role assignment pending verification |
| Built-in Entra Security Reader role | Available |
| `PAG-DEFENDER-SECURITY-OPERATOR` | Created |
| `WF-Defender-Security-Operator` | Not yet implemented |
| `PAG-INTUNE-ENDPOINT-ADMIN` | Created |
| `WF-Intune-Endpoint-Admin` | Custom Intune role created |
| Intune pilot role assignment | Created and configuration verified |
| Dedicated `adm-*` identities | Pending |
| Privileged Identity Management | Available; implementation pending |

This is our actual implementation state.

We have successfully introduced a custom Intune role and connected it to a scoped administrative assignment.

But the wider privileged-access model remains a work in progress.

### The Next Level: Privileged Identity Management

Our IAM design also established that administrative privileges should be controlled, reviewed, and used only when required.

Microsoft Entra Privileged Identity Management (PIM) is part of that longer-term approach.

The objective is to move towards approved, time-bound administrative access where supported.

However, we have not yet configured PIM for the Wire Finance privileged-access groups.

Before doing that, we still need to establish the dedicated administrative identities and complete the relevant platform authorisation arrangements.

We will not treat PIM as implemented simply because Entra ID P2 makes it available.

### What We Have Achieved

The most important outcome of this stage is the separation of business responsibility from administrative permission.

We now have:

- Privileged-access groups representing different administrative functions.
- A custom Intune role defining approved endpoint-management capabilities.
- A pilot role assignment connecting the Intune role to its intended administrative group.
- A defined pilot management scope.
- A record of the privileged-access components that still need implementation.

The design principle is beginning to take shape inside the Microsoft environment.

```text
Business Responsibility
          ↓
Privileged Access Group
          ↓
Platform Authorisation
          ↓
Scoped Administrative Permissions
          ↓
Validation and Review
```

But administrative permissions are only one part of securing an identity.

Even a correctly scoped administrator still needs a secure way to authenticate.

And our standard workforce identities also need appropriate authentication controls before we can trust their sign-ins.

That takes us to the next stage of our implementation.

**Next: Authentication Methods — establishing how Wire Finance users prove their identities when accessing company resources.**