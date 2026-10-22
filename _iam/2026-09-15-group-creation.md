---
layout: lesson
title: "Group Creation"
series: wire-finance
parent: entra-id-implementation
order: 3
description: "Build Wire Finance's Microsoft Entra security groups before creating employees: dynamic company, department and role groups; assigned pilot, exception and privileged groups; and device groups for Intune and Autopilot."
---

We had designed how Wire Finance employees should receive access. Now I needed to turn that design into real Microsoft Entra groups **before adding the employees themselves**.

That order was intentional. An employee's department and role should determine their standard group memberships automatically. Pilot participation, payment-processing exceptions and administrative privilege, however, should never appear merely because a job title matches a rule.

At this point in the lab, I created **19 user/access security groups**. Our device-design work added **five device security groups**, bringing the pre-deployment foundation to **24**.

The licensing group that eventually took the inventory to **25** came later, during User Creation; I'll preserve that sequence rather than pretend we made everything at once.

### 1. Find group creation in Entra

I opened the [Microsoft Entra admin center](https://entra.microsoft.com/) and navigated to:

**Identity → Groups → All groups → New group**

Before reproducing this, check that your tenant has Microsoft Entra ID P1/P2 for dynamic membership and that your administrator can manage groups. Creating the special **role-assignable** Entra group requires an appropriately privileged administrator, such as a **Privileged Role Administrator**. Our tenant already had Entra ID P2 available.

For our normal user and device groups, the basic settings were:

| Setting | Selection |
|---|---|
| Group type | **Security** |
| Group name | The exact `SG-*` or `PAG-*` name shown below |
| Group description | State what the group controls and how membership is granted |
| Microsoft Entra roles can be assigned to the group | **No**, except for the explicitly identified Entra role-assignable group |
| Membership type | **Dynamic User**, **Dynamic Device**, or **Assigned**, according to purpose |

For a dynamic group, selecting **Dynamic User** or **Dynamic Device** exposes **Add dynamic query**.

I used the **Edit rule syntax** text box where necessary, pasted the expression, selected **Save**, then returned to the group page and selected **Create**.

For an **Assigned** group, I did not add a dynamic rule; membership is handled deliberately through **Members → Add members** when we have an approved user or device to add.

> If you are following along, don't confuse **Group type: Security** with **Membership type: Assigned**. A security group can have assigned or dynamic membership. Also, choosing **Microsoft Entra roles can be assigned = Yes** is a separate, high-trust decision that cannot be changed later. Microsoft documents this distinction in [role-assignable groups](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/groups-concept).

### 2. Create the company-wide baseline

The first group was `SG-BASE-ALL-STAFF`.

- **Group type:** Security
- **Membership type:** Dynamic User
- **Entra role assignable:** No

In **Add dynamic query → Edit rule syntax**, I used:

```text
(user.companyName -eq "Wire Finance") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

This rule is deliberately more specific than checking the company name alone. An identity needs to belong to Wire Finance, be a **Member** and be **enabled**.

That matters because our *planned* privileged and emergency identities use different company values, and should not inherit everyday employee access merely because they exist in the same tenant.

The group gives us a workforce population for future company-wide assignments; **creating the group alone does not deploy apps or grant a resource entitlement**.

### 3. Create five dynamic department groups

I repeated the same creation workflow for the five departments. Every one is a **Security / Dynamic User** group, with **Microsoft Entra roles can be assigned = No**.

| Group name | Exact department value |
|---|---|
| `SG-DEPT-FINANCE-USERS` | `Finance` |
| `SG-DEPT-HR-USERS` | `HR` |
| `SG-DEPT-OPERATIONS-USERS` | `Operations` |
| `SG-DEPT-SALES-USERS` | `Sales` |
| `SG-DEPT-IT-USERS` | `IT` |

For each group, the rule is the following expression with the **exact corresponding department from the table**:

```text
(user.companyName -eq "Wire Finance") -and
(user.department -eq "Finance") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

For example, when creating `SG-DEPT-HR-USERS`, I replaced only `"Finance"` with `"HR"`; the company, user type and enabled-account checks stayed the same.

I followed that same pattern for Operations, Sales and IT.

The point was simple: a Finance employee should enter the Finance population based on trusted identity attributes, not because IT remembered to add another group membership.

### 4. Create eight dynamic role groups

Department membership tells us *where* an employee works. It doesn't tell us their precise function.

So I created eight additional **Security / Dynamic User** groups, again with Entra role assignability set to **No**.

| Group name | `department` | Exact `jobTitle` |
|---|---|---|
| `SG-ROLE-FINANCE-ANALYST` | `Finance` | `Finance Analyst` |
| `SG-ROLE-HR-MANAGER` | `HR` | `HR Manager` |
| `SG-ROLE-OPERATIONS-SPECIALIST` | `Operations` | `Operations Specialist` |
| `SG-ROLE-SALES-REPRESENTATIVE` | `Sales` | `Sales Representative` |
| `SG-ROLE-IT-ADMIN` | `IT` | `IT Administrator` |
| `SG-ROLE-SOC-ANALYST` | `IT` | `SOC Analyst` |
| `SG-ROLE-ENDPOINT-ADMIN` | `IT` | `Deployment/Support Engineer` |
| `SG-ROLE-SECURITY-LEADERSHIP` | `IT` | `Head of Security Operations` |

Each role uses **all five conditions**.

For `SG-ROLE-FINANCE-ANALYST`, the exact expression was:

```text
(user.companyName -eq "Wire Finance") -and
(user.department -eq "Finance") -and
(user.jobTitle -eq "Finance Analyst") -and
(user.userType -eq "Member") -and
(user.accountEnabled -eq true)
```

To reproduce the other seven groups, create each named group and replace **both** the `department` and `jobTitle` values with its exact row in the table.

Keep the other three checks unchanged.

For example, `SG-ROLE-ENDPOINT-ADMIN` uses `department = "IT"` and `jobTitle = "Deployment/Support Engineer"`—including the slash.

`SG-ROLE-SECURITY-LEADERSHIP` uses `"Head of Security Operations"`, not a shortened title.

This creates the standard access-population model:

```text
Enabled Wire Finance employee
        ├── SG-BASE-ALL-STAFF
        ├── SG-DEPT-[DEPARTMENT]-USERS
        └── SG-ROLE-[APPROVED ROLE]
```

**Important:** `SG-ROLE-ENDPOINT-ADMIN` is a *business role group*, **not** a grant of Intune administrator privileges.

Platform administration is separately controlled through privileged-access groups, administrator identities and platform RBAC.

### 5. Create the deliberately assigned groups

Some memberships represent approval rather than an employee attribute.

For these, I selected **Security → Assigned** and left dynamic membership rules out of the process.

| Group | Purpose | Entra role assignable |
|---|---|---|
| `SG-PILOT-IDENTITY` | Explicitly selected identity and endpoint-enrollment pilot users | **No** |
| `SG-EXCEPTION-PAYMENT-PROCESSING` | Individually approved payment-processing exception access | **No** |
| `PAG-ENTRA-SECURITY-READER` | Intended holder of the built-in Microsoft Entra Security Reader role | **Yes** |
| `PAG-DEFENDER-SECURITY-OPERATOR` | Intended admin population for later Defender XDR authorization | **No** |
| `PAG-INTUNE-ENDPOINT-ADMIN` | Intended admin population for a custom Microsoft Intune RBAC assignment | **No** |

#### The Entra role-assignable group needs special care

When creating `PAG-ENTRA-SECURITY-READER`, **Microsoft Entra roles can be assigned to the group = Yes**.

Its membership remains **Assigned**.

Microsoft requires this capability to be selected *when the group is created*; it can't be switched on for an existing group later.

The Defender and Intune `PAG-*` groups remained **Entra role assignable = No** because their authorization is configured inside **Defender Unified RBAC** and **Intune RBAC**, not by creating custom directory roles in Entra.

A `PAG-*` name doesn't automatically confer any permissions.

At this stage, the groups existed; the full privileged identities, PIM and Defender role implementation were **not** complete.

We later created and checked the **Intune** custom role assignment separately in the endpoint implementation chapter.

`SG-PILOT-IDENTITY` and the payment-processing exception group also stayed Assigned: we didn't want a matching title to place somebody into a pilot or grant sensitive exception access automatically.

### 6. Account for our five device groups

The device groups came from our Device Identity and Provisioning work.

I include the exact inventory here because these are also Microsoft Entra security groups, and we used them immediately afterward in Intune and Windows Autopilot.

| Device group | Membership type | Job |
|---|---|---|
| `SG-DEVICES-AUTOPILOT-CORPORATE` | Dynamic Device | Devices tagged `CORPORATE` for standard provisioning |
| `SG-DEVICES-WINDOWS-CORPORATE` | Dynamic Device | Active, company-owned, Intune-managed Windows population |
| `SG-DEVICES-PRIVILEGED` | Dynamic Device | Devices classified for the `PAW` provisioning route |
| `SG-DEVICES-WINDOWS-PILOT` | Assigned | Devices explicitly placed in the managed pilot ring |
| `SG-DEVICES-NONCOMPLIANT-TEST` | Assigned | Devices explicitly approved for disruptive compliance testing |

All five were **Security** groups with **Microsoft Entra roles can be assigned = No**.

For the three dynamic device groups, these were the recorded rules.

**Corporate Autopilot classification:**

```text
(device.devicePhysicalIds -any (_ -eq "[OrderID]:CORPORATE"))
```

**Privileged workstation classification:**

```text
(device.devicePhysicalIds -any (_ -contains "[OrderID]:PAW"))
```

**Operational corporate Windows devices:**

```text
(device.deviceOSType -eq "Windows") -and
(device.deviceOwnership -eq "Company") -and
(device.deviceManagementAppId -eq "0000000a-0000-0000-c000-000000000000") -and
(device.accountEnabled -eq true)
```

Notice why we kept separate groups: an Autopilot Group Tag describes **how a device should be provisioned**; it does not establish that the device has already enrolled, is compliant, or is safe to use.

We did **not** manually add the VirtualBox device at this point.

Its later registration with Group Tag `CORPORATE` and resulting dynamic membership are tested in the Windows Autopilot implementation chapter.

### 7. Confirm the inventory and understand what wasn't finished

After building the user and device groups, the implementation checkpoint was:

| Population | Dynamic | Assigned | Total |
|---|---:|---:|---:|
| Workforce/access/privileged security groups | 14 | 5 | **19** |
| Device security groups | 3 | 2 | **5** |
| **Original group foundation** | **17** | **7** | **24** |

I verified the created groups in **Entra → Identity → Groups → All groups**, checking their names, membership types and the special Entra role-assignability setting on the relevant `PAG-*` group.

At that point, many dynamic user groups had no employee members because **we hadn't created the eight pilot employees yet**.

That was expected, not proof that the rules had failed.

Later, immediately before Olivia's first Autopilot deployment, we created those employees, confirmed that company/department/role memberships populated and created one more security group:

`SG-LICENCE-M365-E5` — **Security / Assigned / Entra role assignable = No**.

That brought the actual security-group inventory to **25**: 20 workforce/access/licensing/privileged groups and five device groups.

We assigned Microsoft 365 E5 licensing through that group in **User Creation**, rather than putting licence assignment into the earlier 24-group build.

> **Validation checkpoint:** Before relying on a dynamic group, open its **Dynamic membership rules**, inspect the saved expression and, once test users exist, use **Validate rules** or inspect actual **Members**. Processing is asynchronous, so a newly created identity may not appear instantly. For the full rule-editor workflow, see [Microsoft Learn: Create a dynamic membership group](https://learn.microsoft.com/en-us/entra/identity/users/groups-create-rule). Do not confuse a successfully saved expression with a validated real-world membership result.

### Where this takes us

The group foundation was now ready to support controlled onboarding and device provisioning.

In the original lab sequence, I went next into **Intune RBAC and enrollment foundations**; we then discovered that the actual pilot users still needed to be created before the first employee deployment.

For a clearer reading journey, the next page covers those **eight User Creation records** and shows what happened when we finally gave the dynamic rules real identities to evaluate.

The distinction remains the same throughout the project: **groups define the intended population; licences, policies, application assignments and platform roles define what that population actually receives.**