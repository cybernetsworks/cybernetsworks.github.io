---
layout: lesson
title: "Windows Autopilot Classification"
series: wire-finance
parent: device-identity-provisioning
order: 5
description: "Before a device is provisioned, Wire Finance needs to know what kind of build it should receive. Autopilot classification separates standard corporate endpoints from privileged workstations before deployment begins."
---

We now have device groups capable of separating:

- Provisioning populations
- Operational corporate devices
- Pilot devices
- Privileged workstations
- Controlled test devices

But two of those groups depend on a decision that happens even earlier.

Before a new Windows device completes Microsoft Entra Join.

Before it enrolls into Intune.

Before security policies and applications are applied.

Wire Finance needs to answer:

**What kind of device are we trying to build?**

That is the purpose of **Windows Autopilot classification**.

#### Registration and Classification Are Different

The central idea is simple:

**Autopilot registration identifies the physical device. The Group Tag identifies its intended deployment class.**

Conceptually:

```text
Physical Device
      ↓
Registered with Windows Autopilot
      ↓
Group Tag Assigned
      ↓
Microsoft Entra Dynamic Group Evaluates Classification
      ↓
Correct Autopilot Population Identified
      ↓
Correct Deployment Profile Applied
```

The device exists first.

The classification then tells Wire Finance which provisioning path that device should follow.

That decision happens before the endpoint reaches its normal operational state.

#### Initial Wire Finance Classifications

Wire Finance deliberately begins with only two Autopilot classifications:

| Group Tag | Meaning | Dynamic Group | Deployment Intent |
|---|---|---|---|
| **`CORPORATE`** | Standard employee Windows endpoint | `SG-DEVICES-AUTOPILOT-CORPORATE` | Standard corporate build |
| **`PAW`** | Privileged Access Workstation | `SG-DEVICES-PRIVILEGED` | Hardened privileged build |

The classification decision creates two different provisioning paths before normal device deployment begins.

![Wire Finance Autopilot Classification Model]({{ '/assets/images/active-directory/autopilot_classification_model.png' | relative_url }})

The image brings the full model together:

```text
New Device
      ↓
Autopilot Registered
      ↓
Purpose Confirmed
      ↓
Classification
      ↓
Dynamic Device Group
      ↓
Deployment Profile
      ↓
Microsoft Entra Join + Intune
```

Both paths eventually use the same core cloud identity and management platforms.

What changes is the **provisioning intent and security posture applied before the device reaches that operational state**.

So:

```text
CORPORATE
      ≠
PAW
```

even though both can eventually become:

```text
Microsoft Entra Joined
        +
Intune Managed
```

---

#### How the Group Tag Reaches Microsoft Entra

The Autopilot Group Tag is represented through the device's `OrderID` information.

That gives Microsoft Entra something that dynamic device-group rules can evaluate before normal provisioning has completed.

For Wire Finance:

```text
Group Tag
      ↓
OrderID
      ↓
Dynamic Device Rule
      ↓
Device Group
      ↓
Autopilot Deployment Path
```

This is important because many of the operational attributes we care about later may not exist yet.

Before provisioning completes, the device may not yet be:

```text
Intune Managed

Compliant

Fully Configured

Operational
```

But the provisioning system still needs to know what build it should receive.

The Autopilot classification gives us that early signal.

#### `CORPORATE` Classification

A normal Wire Finance employee endpoint receives:

```text
Group Tag:
CORPORATE
```

The classification appears through:

```text
[OrderID]:CORPORATE
```

and is dynamically identified by:

`SG-DEVICES-AUTOPILOT-CORPORATE`

The implemented rule is:

```text
(device.devicePhysicalIds -any (_ -eq "[OrderID]:CORPORATE"))
```

The relationship becomes:

```text
CORPORATE
      ↓
[OrderID]:CORPORATE
      ↓
SG-DEVICES-AUTOPILOT-CORPORATE
      ↓
Standard Wire Finance
Autopilot Deployment
```

The eventual standard provisioning journey can then lead toward:

```text
Microsoft Entra Join
        +
Automatic Intune Enrollment
        +
Standard Security Controls
        +
Required Applications
        +
Compliance Evaluation
```

The important word here is **intent**.

The `CORPORATE` classification means:

**this device should receive the standard Wire Finance corporate build.**

It does not yet prove that the build was completed successfully.

#### `PAW` Classification

Privileged Access Workstations require a different provisioning path.

They receive:

```text
Group Tag:
PAW
```

Wire Finance dynamically identifies those devices through:

`SG-DEVICES-PRIVILEGED`

The tenant-validated rule is preserved as:

```text
(device.devicePhysicalIds -any (_ -contains "[OrderID]:PAW"))
```

The relationship becomes:

```text
PAW
      ↓
[OrderID]:PAW
      ↓
SG-DEVICES-PRIVILEGED
      ↓
Hardened PAW Deployment
```

This gives Wire Finance a way to identify the privileged workstation **before** the machine becomes a normal operational endpoint.

That matters because the security expectations for a PAW are different from those of an everyday employee laptop.

```text
CORPORATE
      ≠
PAW
```

even though both devices may later be:

```text
Windows
      +
Corporate Owned
      +
Microsoft Entra Joined
      +
Intune Managed
```

The difference is their **intended security and trust function**.

#### Why Classification Happens Before Provisioning

Imagine two physically identical Windows laptops arrive at Wire Finance.

The first is intended for Olivia Carter.

```text
Device A
      ↓
CORPORATE
      ↓
Standard Employee Build
```

The second is intended for privileged administration by Jordan Lee.

```text
Device B
      ↓
PAW
      ↓
Privileged Hardened Build
```

The hardware could be almost identical.

The security requirements are not.

Without a classification decision, we effectively have:

```text
New Laptop
      ↓
Which Build?
      ↓
Unknown
```

With Autopilot classification:

```text
New Laptop
      ↓
Purpose Confirmed
      ↓
Group Tag
      ↓
Correct Device Group
      ↓
Correct Provisioning Path
```

This is why classification needs to exist before deployment rather than being added as an afterthought.

#### Classification Is Not Ownership

There is another important boundary.

```text
Group Tag = CORPORATE
```

means:

**the device has been classified for the standard Wire Finance corporate provisioning path.**

It does not independently prove:

```text
Company Ownership

Intune Management

Compliance

Security Health

Trust
```

Those states are established later.

So:

```text
AUTOPILOT CLASSIFICATION
"What should we build?"
```

is different from:

```text
OPERATIONAL TRUST
"Is the completed device secure enough to use?"
```

This continues the trust model established earlier.

A Group Tag is an input into provisioning.

It is not a certificate of trust.

#### Classification Is Security-Sensitive Metadata

The classification may look like a small label:

```text
CORPORATE

PAW
```

but those values influence dynamic group membership.

Dynamic group membership can influence the deployment profile and security controls that a device receives.

So the chain becomes:

```text
Group Tag
      ↓
Dynamic Group Membership
      ↓
Deployment Profile
      ↓
Configuration
      ↓
Security Posture
```

That makes the Group Tag **security-sensitive provisioning metadata**.

The employee receiving the laptop should not decide which classification it receives.

#### Classification Authority

Wire Finance therefore defines who is allowed to make classification decisions.

| Action | Authority |
|---|---|
| **Register standard corporate device** | IT / Endpoint Management |
| **Assign `CORPORATE` tag** | IT / Endpoint Management |
| **Assign `PAW` tag** | IT / Endpoint Management + Security approval |
| **Change `CORPORATE` → `PAW`** | IT + Security |
| **Change `PAW` → `CORPORATE`** | IT + Security |
| **Remove Autopilot classification** | IT / Endpoint Management |
| **Validate PAW classification** | Security |
| **Audit classification changes** | IT / Security |

The additional approval around PAWs reflects the stronger security purpose of those devices.

A normal employee cannot simply decide:

```text
I want this laptop to be a PAW.
```

Likewise, privileged equipment should not be downgraded into an ordinary employee device without a controlled process.

#### Standard Corporate Classification Workflow

For a normal employee device, the expected decision path is:

```text
New Device Acquired
      ↓
Asset Recorded
      ↓
Purpose Confirmed
      ↓
Standard Employee Endpoint
      ↓
Registered with Windows Autopilot
      ↓
CORPORATE Tag Assigned
      ↓
Dynamic Rule Evaluates
      ↓
SG-DEVICES-AUTOPILOT-CORPORATE
      ↓
Standard Deployment Profile
```

Classification therefore sits between **asset purpose** and **technical provisioning**.

The organisation first decides what the device is for.

Then the technology implements that decision.

#### Privileged Workstation Classification Workflow

A PAW requires an additional governance step.

```text
Privileged Workstation Required
      ↓
Business / Security Requirement Confirmed
      ↓
Security Approval
      ↓
Registered with Windows Autopilot
      ↓
PAW Tag Assigned
      ↓
SG-DEVICES-PRIVILEGED
      ↓
Hardened PAW Provisioning
```

This makes the PAW classification deliberate.

The stronger build exists because there is an approved privileged use case behind it.

---

#### What Happens If the Wrong Group Tag Is Assigned?

This is where classification becomes more than organisation.

Imagine:

```text
WF-PAW-001
```

is accidentally classified as:

```text
CORPORATE
```

instead of:

```text
PAW
```

The device could enter the standard corporate provisioning population instead of the privileged-device population.

That creates a problem.

The hostname may still say:

```text
WF-PAW-001
```

but, as we established earlier:

**a device name does not create the security state.**

The wrong provisioning classification may mean the device received the wrong build.

Wire Finance therefore does not treat misclassification as:

```text
Change the Tag
      ↓
Carry On
```

The response is:

```text
Misclassification Detected
        ↓
Stop Deployment / Privileged Use
        ↓
Correct Asset Classification
        ↓
Correct Autopilot Group Tag
        ↓
Verify Dynamic Group Membership
        ↓
Reset / Reprovision If Incorrect Build Applied
        ↓
Validate Correct Security Baseline
        ↓
Return Device to Service
```

For a PAW especially, the safest design assumption is:

**if the device received the wrong security build, return it to a known-good state before privileged use.**

#### Reclassification Is a Change of Purpose

The same principle applies after deployment.

Changing:

```text
CORPORATE
      ↓
PAW
```

is not simply changing a text label.

The existing machine was already built according to the `CORPORATE` classification.

The device purpose has now materially changed.

The expected process therefore becomes:

```text
Change of Device Purpose
        ↓
Approval
        ↓
Update Autopilot Classification
        ↓
Verify Group Membership
        ↓
Reset / Reprovision
        ↓
Correct Profile Applied
        ↓
Security State Validated
        ↓
Return to Service
```

The reverse also matters.

```text
PAW
      ↓
Standard Corporate Endpoint
```

should not mean handing the privileged workstation directly to an employee for everyday use.

Before reassignment, the machine should be brought back through a controlled provisioning process so privileged workstation state does not silently follow it into its new role.

#### Classification Values Must Stay Predictable

Wire Finance uses uppercase values for Autopilot classifications.

Current approved values are:

```text
CORPORATE

PAW
```

We deliberately avoid creating inconsistent variations such as:

```text
Corp
corporate
CorporateDevice
AdminLaptop
PAW2
VIP
IT
```

because uncontrolled classification values make automation harder to understand and govern.

New Group Tags should be introduced only when a **genuinely different provisioning path** exists.

For example, a future shared or kiosk-device architecture might eventually justify another classification.

But a new department does not automatically need a new Autopilot tag.

#### Why There Is No `FINANCE`, `HR`, or `SALES` Tag

Autopilot classification is not intended to reproduce the organisational chart.

For example:

```text
FINANCE
HR
OPERATIONS
SALES
IT
```

do not currently need separate Autopilot classifications.

Why?

Because those employees can still receive the same underlying **standard corporate device build**.

```text
Finance Employee
      ↓
CORPORATE

HR Employee
      ↓
CORPORATE

Sales Employee
      ↓
CORPORATE
```

Department-specific applications or access can be handled through other targeting mechanisms later.

The Autopilot Group Tag remains focused on:

**Does this device require a materially different provisioning architecture?**

That prevents the classification model from becoming unnecessarily complicated.

#### Why Only Two Classifications?

Wire Finance currently has only two materially different foundational endpoint models:

```text
STANDARD CORPORATE
```

and:

```text
PRIVILEGED
```

The pilot and noncompliance-test groups do not require separate Autopilot tags.

That is because:

```text
PILOT
```

describes **how a device is temporarily being used during rollout**.

And:

```text
NONCOMPLIANT TEST
```

describes **a controlled testing function**.

Neither necessarily requires a completely different base build.

For example:

```text
WF-TST-001
      ↓
Autopilot Tag = CORPORATE
      ↓
Standard Corporate Base Build
      ↓
Additionally Assigned To:
SG-DEVICES-WINDOWS-PILOT
        +
SG-DEVICES-NONCOMPLIANT-TEST
```

That gives us a cleaner model than creating a different Autopilot tag for every lab activity.

#### Classification and Device Groups

The architecture now connects cleanly:

```text
Purpose
      ↓
Autopilot Classification
      ↓
Dynamic Device Group
      ↓
Deployment Profile
      ↓
Provisioned Device
```

For the standard path:

```text
CORPORATE
      ↓
SG-DEVICES-AUTOPILOT-CORPORATE
      ↓
Standard Corporate Deployment
```

For the privileged path:

```text
PAW
      ↓
SG-DEVICES-PRIVILEGED
      ↓
Hardened PAW Deployment
```

The Group Tag determines the provisioning classification.

The dynamic group converts that classification into a targetable population.

The deployment profile can then act on that population.

That gives Wire Finance a chain that can be understood and audited.

#### Pre-Provisioning Validation

Before an important device is provisioned, Endpoint Management should be able to verify the expected classification.

| Check | Standard Endpoint | PAW |
|---|---|---|
| **Device registered in Autopilot** | Yes | Yes |
| **Asset ownership confirmed** | Yes | Yes |
| **Group Tag present** | `CORPORATE` | `PAW` |
| **Expected dynamic group** | `SG-DEVICES-AUTOPILOT-CORPORATE` | `SG-DEVICES-PRIVILEGED` |
| **Expected deployment profile** | Standard | Hardened |
| **Intended user / purpose confirmed** | Yes | Yes |
| **Security approval** | Normal IT process | Required |

This gives Wire Finance a small pre-flight check before deployment begins.

The goal is simple:

**catch a classification problem before it becomes a provisioning problem.**

#### Wire Finance Autopilot Classification Standard

The current standard can therefore be recorded as:

```text
Classification:
CORPORATE

Purpose:
Standard Employee Windows Provisioning

Dynamic Group:
SG-DEVICES-AUTOPILOT-CORPORATE

Dynamic Rule:
(device.devicePhysicalIds -any (_ -eq "[OrderID]:CORPORATE"))

Approval:
IT / Endpoint Management
```

and:

```text
Classification:
PAW

Purpose:
Privileged Access Workstation Provisioning

Dynamic Group:
SG-DEVICES-PRIVILEGED

Dynamic Rule:
(device.devicePhysicalIds -any (_ -contains "[OrderID]:PAW"))

Approval:
IT / Endpoint Management + Security
```

The governing principle is:

**Autopilot classification determines provisioning intent; it does not independently establish device trust.**

And when that classification changes the security purpose of an existing device, Wire Finance treats it as more than a label change.

It requires controlled reprovisioning and validation.

#### We Know the Build — Now We Need the Journey

At this point, the standard corporate device has a clear identity before provisioning begins.

```text
Corporate Asset
      ↓
Autopilot Registered
      ↓
CORPORATE
      ↓
SG-DEVICES-AUTOPILOT-CORPORATE
      ↓
Standard Deployment Path
```

We know **which build** the device should receive.

But we have not yet followed that build from beginning to end.

What happens after the device is acquired?

When is it registered?

When does the employee sign in?

When does Microsoft Entra Join happen?

When does Intune take control?

When are applications and security controls delivered?

And when do we finally consider the laptop ready to hand to the employee?

That brings us to the next part of the design:

**the Standard Corporate Provisioning Journey.**