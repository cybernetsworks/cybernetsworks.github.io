---
layout: lesson
title: "Privileged Workstation Provisioning"
series: wire-finance
parent: device-identity-provisioning
order: 7
description: "Privileged administration needs more than a separate admin account. Follow how Wire Finance provisions, hardens, validates, and governs a dedicated Privileged Access Workstation."
---

The standard corporate provisioning journey gives Wire Finance a repeatable way to turn a new Windows laptop into a managed, secured, and compliant employee endpoint.

But privileged administration raises the security bar.

A workstation used to administer identities, endpoints, security tools, or other sensitive systems should not simply be:

```text
Normal Laptop
      +
Extra Permissions
```

Wire Finance uses a separate **Privileged Access Workstation**, or PAW, for approved administrative activity.

The principle is:

**a Privileged Access Workstation is not just another company laptop with extra permissions. It is a separately classified, hardened device dedicated to administrative activity.**

#### Our Privileged Example

We will follow Jordan Lee through the PAW model.

```text
Employee:
Jordan Lee

Normal Identity:
jordan.lee@wirefinance.com

Privileged Identity:
adm-jordan.lee@wirefinance.com

Privileged Device:
WF-PAW-001

Autopilot Classification:
PAW

Provisioning Group:
SG-DEVICES-PRIVILEGED
```

Jordan still has a normal employee identity and standard corporate endpoint for everyday work.

The PAW exists for something else entirely:

```text
Approved Administrative Activity
```

That distinction is fundamental to the Wire Finance privileged-access model.

#### PAW Design Standard

The initial PAW design is:

| Decision | Wire Finance PAW Standard |
|---|---|
| **Ownership** | Corporate |
| **Device type** | Privileged Access Workstation |
| **Naming** | `WF-PAW-###` |
| **Autopilot Group Tag** | `PAW` |
| **Provisioning group** | `SG-DEVICES-PRIVILEGED` |
| **Join type** | Microsoft Entra Joined |
| **Management** | Microsoft Intune |
| **Provisioning** | Windows Autopilot |
| **User account type** | Standard local rights |
| **Primary identity** | Approved `adm-*` identity |
| **Normal productivity** | Not permitted |
| **Security baseline** | Dedicated hardened PAW baseline |
| **Compliance** | Dedicated privileged-device compliance |
| **Conditional Access** | Required for selected privileged operations later |

There is one important detail here.

A PAW still uses:

```text
Microsoft Entra Join
        +
Microsoft Intune
```

just like a normal corporate laptop.

The stronger security posture does **not** come from inventing a special join type.

It comes from the complete device design around it:

```text
Different Classification
        +
Different Provisioning Profile
        +
Stricter Security Controls
        +
Dedicated Compliance
        +
Restricted Usage
        +
Privileged Identity
```

So:

```text
Same Cloud Identity Platform
        ≠
Same Security Tier
```

---

#### A PAW Starts With a Privileged Requirement

A normal employee laptop may be issued because somebody joins the organisation and needs a productivity device.

A PAW should not be issued simply because:

```text
Jordan Works in IT
```

That is not enough.

There must be an approved administrative requirement.

For example:

```text
Jordan Lee
Head of Security Operations
        ↓
Approved Privileged Responsibilities
        ↓
Separate adm-jordan.lee Identity
        ↓
Dedicated PAW Required
```

The PAW exists because the **privileged-access model requires it**.

That gives us a clean chain:

```text
Business Responsibility
        ↓
Privileged Requirement
        ↓
Approved Privileged Identity
        ↓
Approved Privileged Device
```

This prevents privileged workstations from becoming ordinary equipment handed out because someone happens to have a technical job title.

#### The Privileged Workstation Journey

The PAW journey begins before the device is provisioned.

Wire Finance first establishes that a genuine privileged-administration requirement exists.

Only then is the device approved, classified, provisioned, hardened, and validated for administrative use.

![Wire Finance Privileged Workstation Provisioning Journey]({{ '/assets/images/active-directory/privileged_workstation_provisioning_journey.png' | relative_url }})

The journey can be understood in four broad phases:

```text
Approval
   ↓
Provisioning
   ↓
Hardening
   ↓
Privileged Trust Validation
```

A PAW therefore does not become suitable for administrative work simply because it has been purchased, named, or joined to Microsoft Entra ID.

It must successfully move through the entire security journey before Wire Finance approves it for privileged use.

It resembles the standard corporate journey, but the security purpose and acceptance criteria are much stricter.

#### Stage 1 — Record the Privileged Asset

The PAW receives an asset record just like every other Wire Finance device.

But additional security-sensitive information needs to be captured.

For `WF-PAW-001`:

| Field | Value |
|---|---|
| **Device name** | `WF-PAW-001` |
| **Ownership** | Corporate |
| **Device class** | Privileged Access Workstation |
| **Assigned administrator** | Jordan Lee |
| **Privileged identity** | `adm-jordan.lee` |
| **Autopilot classification** | `PAW` |
| **Lifecycle status** | Provisioning |
| **Security tier** | Privileged |

The important field is:

```text
Device Class = PAW
```

That is not merely an inventory description.

It is a **security-sensitive business classification**.

It influences how the device must be provisioned, configured, managed, and eventually retired.

#### Stage 2 — Register the PAW with Windows Autopilot

`WF-PAW-001` is registered with Windows Autopilot.

It receives:

```text
Group Tag:
PAW
```

That classification is represented through:

```text
[OrderID]:PAW
```

and drives the existing dynamic rule:

```text
(device.devicePhysicalIds -any (_ -contains "[OrderID]:PAW"))
```

The result is:

```text
WF-PAW-001
      ↓
PAW Classification
      ↓
SG-DEVICES-PRIVILEGED
```

This separates the device from the standard corporate provisioning population before the main build begins.

#### Stage 3 — Use a Dedicated PAW Deployment Profile

Wire Finance should not apply:

```text
WF-AP-CORPORATE-USER-DRIVEN
```

to its privileged workstations.

Instead, the PAW requires its own deployment profile.

A planned name is:

```text
WF-AP-PAW-USER-DRIVEN
```

The distinction is deliberate:

```text
Standard Employee Build
        ≠
Privileged Administrative Build
```

even though both may use:

```text
Windows Autopilot
Microsoft Entra Join
Microsoft Intune
```

The provisioning technology can remain the same while the resulting security posture is materially different.

#### Stage 4 — Microsoft Entra Join and Intune Enrollment

The PAW becomes:

```text
Microsoft Entra Joined
        +
Microsoft Intune Managed
```

That is the same identity and management foundation used by normal employee laptops.

So the difference is not:

```text
WF-LT-001
→ Normal Join

WF-PAW-001
→ Special Privileged Join
```

Instead:

```text
WF-LT-001
      ↓
Entra Joined
      ↓
Intune Managed
      ↓
Standard Corporate Trust Tier
```

while:

```text
WF-PAW-001
      ↓
Entra Joined
      ↓
Intune Managed
      ↓
Privileged Trust Tier
```

The trust tier comes from the full control set applied to the device.

---

#### Stage 5 — Apply the Hardened PAW Configuration

This is where the PAW begins to separate significantly from the standard corporate endpoint.

Planned PAW controls may eventually include:

- BitLocker
- Microsoft Defender for Endpoint
- Windows Firewall
- Stronger attack-surface-reduction policies
- Windows Hello for Business
- Windows LAPS
- Strict application controls
- Restricted browser use
- Restricted productivity applications
- Limited software installation
- Stricter update requirements
- Dedicated compliance requirements
- Tighter Conditional Access controls

The exact settings will be created later during Microsoft Intune implementation.

At this point, we are defining the design requirement rather than pretending those policies already exist.

The principle is:

**PAWs receive a stricter security configuration than standard Wire Finance endpoints.**

#### A PAW Is Not a Productivity Device

This is one of the most important rules in the entire design.

Earlier, we separated:

```text
Normal Identity
        ≠
Privileged Identity
```

The same principle now extends to the endpoint.

Jordan should not use:

```text
WF-PAW-001
```

for routine activities such as:

- Everyday email
- Normal Teams conversations
- General web browsing
- Personal websites
- Routine Office productivity
- Ordinary business activity

Instead:

```text
jordan.lee
      ↓
Standard Corporate Endpoint
      ↓
Normal Productivity
```

while:

```text
adm-jordan.lee
      ↓
WF-PAW-001
      ↓
Privileged Administration
```

We now have separation at **both layers**:

```text
Identity Separation
        +
Device Separation
```

That is much stronger than separating the account while allowing both identities to operate from the same everyday workstation.

#### The Privileged Access Chain

The PAW does **not** grant administrative privilege by itself.

This distinction matters.

A user possessing `WF-PAW-001` should not automatically gain an administrative role.

Device trust and authorization remain separate controls.

The intended Wire Finance access chain is:

```text
Jordan Lee
│
├── jordan.lee
│      ↓
│   Standard Corporate Endpoint
│      ↓
│   Normal Employee Activity
│
└── adm-jordan.lee
       ↓
   WF-PAW-001
       ↓
   Strong Authentication
       ↓
   PIM Activation
       ↓
   PAG-*
       ↓
   Administrative Role
       ↓
   Privileged Service
```

This is significantly stronger than:

```text
Normal Laptop
      +
Normal User Identity
      +
Permanent Administrator Role
```

which is precisely the architecture Wire Finance is trying to avoid.

#### Device Trust and Authorization Are Different

The PAW proves something about the **device**.

The `adm-*` identity proves something about the **person's administrative identity**.

PIM governs when an eligible privilege becomes active.

The `PAG-*` model connects approved administrators to the relevant administrative authorization.

So:

```text
Approved Privileged Identity
        +
Approved Privileged Device
        +
Strong Authentication
        +
Active Authorized Role
        ↓
Privileged Administration
```

One control should not replace all the others.

For example:

```text
Compliant PAW
      ≠
Administrator Automatically Authorized
```

and:

```text
Privileged Identity
      ≠
Any Device Is Acceptable
```

The controls become stronger when they reinforce each other.

#### Dedicated PAW Compliance

A standard corporate laptop and a PAW should not necessarily have identical compliance requirements.

The PAW will eventually receive its own compliance policy.

Conceptually:

```text
WF-PAW-001
      ↓
Privileged Device Compliance Policy
      ↓
Required Security Controls Present?
      ↓
Compliant / Noncompliant
```

The requirements can be stricter because the device is intended to perform more sensitive actions.

Later, Conditional Access can potentially require a combination such as:

```text
Approved adm-* Identity
        +
Strong Authentication
        +
Compliant Privileged Workstation
        +
PIM Activation
        ↓
Sensitive Administrative Access
```

That is where the identity, device, authentication, and privilege designs begin to lock together.

#### A Device Name Still Does Not Create a PAW

We established earlier that:

```text
WF-PAW-001
```

is a naming convention.

It describes the intended function.

It does not prove the machine received the hardened PAW build.

Likewise:

```text
Group Tag = PAW
```

describes provisioning intent.

It does not independently establish trust.

The completed privileged trust chain requires:

```text
Approved PAW Classification
        +
Correct Provisioning
        +
Microsoft Entra Join
        +
Intune Management
        +
Hardened Configuration
        +
Compliance
        +
Approved Privileged Identity
```

Only the complete state matters.

---

#### What If the PAW Is Misclassified?

Imagine:

```text
WF-PAW-001
```

accidentally receives:

```text
CORPORATE
```

instead of:

```text
PAW
```

The device could receive the normal employee build.

Changing the Group Tag afterwards would not magically apply every security control that should have existed from the beginning.

The response is:

```text
Misclassification Detected
        ↓
Stop Privileged Use
        ↓
Correct Device Classification
        ↓
Correct Autopilot Group Tag
        ↓
Verify SG-DEVICES-PRIVILEGED Membership
        ↓
Reset / Reprovision
        ↓
Apply PAW Deployment Profile
        ↓
Verify Hardened Baseline
        ↓
Verify Compliance
        ↓
Return to Service
```

Wire Finance does **not** simply change:

```text
CORPORATE → PAW
```

and assume the workstation is now hardened.

The machine must be returned to a known-good privileged state.

#### What If the PAW Is Suspected of Compromise?

A privileged workstation deserves a more cautious response than an ordinary support issue.

If its integrity becomes questionable, the security assumption behind privileged administration has also become questionable.

The design response is:

```text
Compromise Suspected
        ↓
Privileged Use Stopped
        ↓
Security Investigation
        ↓
Device Isolated Where Appropriate
        ↓
Reset / Reprovision
        ↓
Known-Good PAW Configuration
        ↓
Security and Compliance Validation
        ↓
Return to Service
```

The principle is:

**when confidence in the privileged endpoint is lost, rebuild trust rather than casually repairing around it.**

#### Reassigning a PAW

A PAW should not simply be handed to another employee.

If the device is no longer required for privileged use:

```text
PAW No Longer Required
        ↓
Privileged Assignment Removed
        ↓
Device Reset / Wiped
        ↓
Reprovision
        ↓
Correct New Classification Applied
        ↓
Validate New State
        ↓
Reassign
```

Likewise:

```text
Normal Corporate Laptop
        ↓
Becomes PAW
```

should require a controlled reprovisioning process.

A change of security purpose requires a change of trusted state.

It is not merely an asset-record edit.

#### PAW Ready-for-Use Gate

The standard corporate device already has a ready-for-use check.

The privileged workstation needs an even stricter one.

Before `WF-PAW-001` is approved for administrative use:

| Validation | Required |
|---|---|
| **Corporate asset record exists** | Yes |
| **PAW requirement and approval recorded** | Yes |
| **Autopilot registration confirmed** | Yes |
| **Group Tag = `PAW`** | Yes |
| **`SG-DEVICES-PRIVILEGED` membership confirmed** | Yes |
| **Microsoft Entra Joined** | Yes |
| **Intune enrolled** | Yes |
| **Dedicated PAW security baseline applied** | Yes |
| **Encryption confirmed** | Yes |
| **Microsoft Defender health acceptable** | Yes |
| **Dedicated compliance state acceptable** | Yes |
| **Assigned administrator confirmed** | Yes |
| **Approved `adm-*` identity confirmed** | Yes |
| **Normal productivity use prohibited** | Yes |

Only after these checks succeed do we reach:

```text
WF-PAW-001
      ↓
Correctly Classified
      +
Entra Joined
      +
Intune Managed
      +
Hardened
      +
Compliant
      +
Approved Administrator
      ↓
APPROVED FOR
PRIVILEGED ADMINISTRATION
```

That is a much higher bar than simply proving the laptop boots and can reach Microsoft 365.

#### Wire Finance PAW Provisioning Decision

The PAW design can now be recorded as:

```text
Device Type:
Privileged Access Workstation

Naming:
WF-PAW-###

Ownership:
Corporate

Provisioning:
Windows Autopilot

Autopilot Classification:
PAW

Provisioning Group:
SG-DEVICES-PRIVILEGED

Planned Autopilot Profile:
WF-AP-PAW-USER-DRIVEN

Join Type:
Microsoft Entra Joined

Management:
Microsoft Intune

Primary Identity:
Approved adm-* Identity

Local Rights:
Least Privilege

Normal Productivity:
Prohibited

Security Baseline:
Dedicated Hardened PAW Baseline

Compliance:
Dedicated Privileged-Device Compliance

Privileged Access:
Approved PAW + Strong Authentication + PIM + PAG-*

Lifecycle:
Controlled Provisioning, Reassignment and Reprovisioning
```

The governing principle is:

**a PAW is a dedicated trusted administrative endpoint. Naming or classification alone does not establish privileged trust; the device must be correctly provisioned, managed, secured, compliant, and used with an approved privileged identity.**

#### Not Every Special Device Is a PAW

We now have two provisioning journeys.

```text
WF-LT-###
      ↓
Standard Employee Productivity
```

and:

```text
WF-PAW-###
      ↓
Privileged Administration
```

But there is another class of device we designed earlier:

```text
WF-TST-###
```

Those devices exist for controlled rollout, testing, and deliberate security validation.

And there is an important distinction we need to make next.

A **pilot device** is used to prove that a change works safely.

A **test device** may deliberately be made to fail so we can observe whether the security controls respond correctly.

Those are very different objectives.

**So how should Wire Finance separate safe staged rollout from deliberate failure testing without putting ordinary production endpoints at risk?**