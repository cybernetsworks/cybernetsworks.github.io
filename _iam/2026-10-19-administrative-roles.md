---
layout: lesson
title: "Administrative Roles"
series: wire-finance
parent: entra-id-implementation
order: 5
description: "Separate Microsoft Entra directory roles from Intune and Defender permissions, and verify which Wire Finance privileged-access assignments were actually implemented."
---

We had created our security groups and, later, the eight standard pilot employees. But there was a question our group design couldn't answer on its own:

**If someone belongs to `PAG-INTUNE-ENDPOINT-ADMIN`, what can they actually do?**

At first glance, the group name sounds like a permission. It isn't. A Microsoft Entra security group identifies a population; an administrative **role assignment** is what connects that population to permissions in a particular platform.

That distinction mattered because Wire Finance uses **three different authorization systems**, not one universal Microsoft administrator role.

### 1. Keep three administrative platforms separate

| Platform | Authorization we planned | Where it belongs | State at this checkpoint |
|---|---|---|---|
| Microsoft Entra ID | Built-in **Security Reader** for `PAG-ENTRA-SECURITY-READER` | Entra directory roles | Group created; role assignment **pending verification** |
| Microsoft Intune | Custom `WF-Intune-Endpoint-Admin` for `PAG-INTUNE-ENDPOINT-ADMIN` | Intune RBAC | **Role created and pilot assignment verified** |
| Microsoft Defender XDR | Custom `WF-Defender-Security-Operator` for `PAG-DEFENDER-SECURITY-OPERATOR` | Defender Unified RBAC | **Not yet created** |

We deliberately created **no custom Microsoft Entra directory role** for endpoint management. We also did not turn the everyday `SG-ROLE-IT-ADMIN` or `SG-ROLE-ENDPOINT-ADMIN` groups into directory administrators.

Those `SG-ROLE-*` groups represent someone's **business function**. The `PAG-*` groups represent separately approved **privileged access**. A job-title update must not silently become an administrator assignment.

### 2. What we confirmed in Microsoft Entra

In the [Microsoft Entra admin center](https://entra.microsoft.com/), I could inspect our privileged groups under:

**Identity → Groups → All groups → [group name] → Properties**

The group settings we had established were:

| Group | Membership | Microsoft Entra roles can be assigned |
|---|---|---|
| `PAG-ENTRA-SECURITY-READER` | Assigned | **Yes** |
| `PAG-INTUNE-ENDPOINT-ADMIN` | Assigned | **No** |
| `PAG-DEFENDER-SECURITY-OPERATOR` | Assigned | **No** |

Only the first group needed to be **Entra role-assignable**, because the built-in **Security Reader** is an Entra directory role. That setting was chosen **when the group was created**; it isn't a switch to turn on casually later.

However, a role-assignable group is still only a group. The original implementation record **does not confirm that we assigned Security Reader to it**.

When we return to privileged-access implementation, the verification route will be:

**Entra admin center → Identity → Roles & admins → Roles and administrators → Security Reader → Assignments**

Check for `PAG-ENTRA-SECURITY-READER` before describing that authorization as active. If it is absent, its assignment will require an appropriately authorized administrator and a separate implementation decision. We're not presenting that pending step as something we already completed.

For Microsoft's procedure, see [Assign Microsoft Entra roles](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/manage-roles-portal).

### 3. What we actually implemented in Intune

When the endpoint-management build resumed, we stopped relying on a planned role name and created a real **Intune custom RBAC role**.

The actual tenant navigation was:

**[Intune admin center](https://intune.microsoft.com/) → Tenant administration → Roles → All roles → Create → Intune role**

Our role was named:

`WF-Intune-Endpoint-Admin`

Its **v1 permission intent** was to support routine pilot endpoint operations, such as viewing and updating managed devices, working with configuration and compliance policies, deploying applications and performing approved support tasks. We selected limited enrollment, security-baseline, endpoint-protection and reporting capabilities too.

We deliberately **excluded** destructive and sensitive powers, including device wipe, retire and delete, access to LAPS or other sensitive credentials, destructive Autopilot deletion and administration of Intune RBAC itself.

The original record preserves those selected **capability categories** but not an independently verified screenshot of every permission checkbox. Therefore I won't invent a full action-by-action permission matrix here. We'll revisit the actual role's **Properties → Permissions** in our Chapter 8 Intune RBAC walkthrough and record each enabled action precisely.

#### The pilot role assignment

After creating the custom role, we created this assignment:

`WF-ASSIGN-INTUNE-ENDPOINT-ADMIN-PILOT`

| Assignment element | Saved value | What it determines |
|---|---|---|
| Intune role | `WF-Intune-Endpoint-Admin` | **What** actions the role permits |
| Admin group | `PAG-INTUNE-ENDPOINT-ADMIN` | **Who** receives the role through group membership |
| Scope (Groups) | `SG-DEVICES-WINDOWS-PILOT` | **Which device population** can be managed through this assignment |
| Scope tag | `Default` | **Which tagged Intune objects** can be visible to the assigned administrators |

![Verified pilot Intune role assignment]({{ '/assets/images/entra-id-implementation/intune-rbac-pilot-assignment-verified.png' | relative_url }})

*Figure: Original assignment verification. `PAG-INTUNE-ENDPOINT-ADMIN` and `SG-DEVICES-WINDOWS-PILOT` are both shown as Active, with the Default scope tag.*

I reopened the assignment after creating it and checked those values rather than trusting the creation wizard alone.

To repeat that validation:

**Intune admin center → Tenant administration → Roles → All roles → WF-Intune-Endpoint-Admin → Assignments → WF-ASSIGN-INTUNE-ENDPOINT-ADMIN-PILOT**

Inspect **Members/Admin Groups**, **Scope (Groups)** and **Scope tags**. Do not confuse the **admin group** with the **scope group**: putting an employee in `SG-DEVICES-WINDOWS-PILOT` does **not** make them an endpoint administrator. That group identifies devices eligible for pilot management.

Also, `Default` is a scope tag, **not an extra permission** or proof of a fully isolated administrative boundary. Intune roles, group scopes and tags work together, and any other role assignments an administrator holds may affect their effective access. See [Microsoft Intune RBAC overview](https://learn.microsoft.com/en-us/intune/fundamentals/role-based-access-control/overview).

**What we verified:** the custom role existed and the saved pilot assignment referenced the correct groups and tag. **What we had not yet verified:** effective permissions by signing in with a dedicated `adm-*` account and demonstrating permitted and denied actions. That test is still required before treating the design as a production-grade least-privilege control.

### 4. Why Defender is not configured here

`PAG-DEFENDER-SECURITY-OPERATOR` was created as an **Assigned** Entra security group, but the planned custom authorization object `WF-Defender-Security-Operator` was **not created** during this stage.

Defender XDR uses its own **Unified RBAC** model. That is the place to select security-operations capabilities, establish data scopes and associate authorized users or groups. It is not an Intune custom role or an Entra custom directory role.

When we reach that implementation, the Microsoft Defender portal's **Permissions → Microsoft Defender XDR → Roles** area is the appropriate starting point. Creating a role is not the same as activating the relevant Unified RBAC workload or validating the resulting access; those steps will need their own evidence.

Reference: [Microsoft Defender Unified RBAC](https://learn.microsoft.com/en-us/defender-xdr/manage-rbac).

### 5. What remained unfinished

The three `PAG-*` groups were a **foundation**, not completed privileged-access operations.

At this point we had **not**:

- Created and validated dedicated `adm-*` privileged user accounts or their memberships.
- Activated Privileged Identity Management (PIM) for time-limited administrative access.
- Verified the Entra **Security Reader** group-to-role assignment.
- Created `WF-Defender-Security-Operator` in Defender Unified RBAC.
- Tested the Intune custom role from the perspective of an actual assigned administrator.
- Implemented and tested emergency-access accounts.

We were still able to use an appropriately authorized tenant administrator for the **setup phase**. That bootstrap access does not prove the delegated, least-privileged operating model has been tested.

#### The checkpoint

Our **Intune administrative role and scoped pilot assignment were created and their configuration verified**. The Entra Security Reader assignment, Defender role and privileged-identity lifecycle remained pending.

This separation gives us an honest account of progress: creating a group, configuring a platform role and validating effective access are **three different milestones**.

Next we'll examine how our standard users authenticate, including the temporary-password process used before Olivia Carter's first Autopilot sign-in—and which authentication controls we deliberately postponed.