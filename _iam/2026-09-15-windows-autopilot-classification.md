---
layout: lesson
title: "Windows Autopilot Classification"
series: wire-finance
parent: device-identity-provisioning
order: 5
description: "Before a device is provisioned, Wire Finance needs to know what kind of build it should receive. Autopilot classification separates standard corporate endpoints from privileged workstations before deployment begins."
---

Wire Finance now has device groups capable of separating different endpoint populations.

But some of those groups depend on a decision that happens even earlier.

Before a new Windows device completes Microsoft Entra Join.

Before it enrolls into Microsoft Intune.

Before applications and security policies are applied.

Wire Finance needs to answer:

**What kind of device are we trying to build?**

That is the purpose of **Windows Autopilot classification**.

### Registration and Classification Are Different

Autopilot registration and Autopilot classification answer different questions.

Registration tells the provisioning service:

**Which physical device is this?**

Classification tells Wire Finance:

**What kind of build should this device receive?**

Conceptually:

```text
Physical Device
      ↓
Registered with Windows Autopilot
      ↓
Group Tag Assigned
      ↓
Dynamic Device Group Evaluates Classification
      ↓
Correct Provisioning Population Identified
      ↓
Correct Deployment Path Applied
```

The device is registered first.

Its classification then tells Wire Finance which provisioning path it should follow.

That classification exists before the endpoint reaches its normal operational state.

### Initial Wire Finance Classifications

Wire Finance deliberately begins with only two Autopilot classifications.

| Group Tag | Meaning | Dynamic Group | Provisioning Intent |
|---|---|---|---|
| `CORPORATE` | Standard employee Windows endpoint | `SG-DEVICES-AUTOPILOT-CORPORATE` | Standard corporate build |
| `PAW` | Privileged Access Workstation | `SG-DEVICES-PRIVILEGED` | Hardened privileged build |

![Wire Finance Autopilot Classification Model]({{ '/assets/images/active-directory/autopilot_classification_model.png' | relative_url }})

The model is:

```text
New Device
      ↓
Autopilot Registered
      ↓
Purpose Confirmed
      ↓
Group Tag
      ↓
Dynamic Device Group
      ↓
Deployment Path
      ↓
Microsoft Entra Join + Intune Enrollment
```

Both device classes may eventually become:

```text
Windows
      +
Corporate Owned
      +
Microsoft Entra Joined
      +
Intune Managed
```

But they do not have the same purpose.

```text
CORPORATE
      ≠
PAW
```

The difference begins with **provisioning intent**.

### How the Group Tag Drives Classification

For Autopilot-registered devices, the Group Tag is exposed through the device's physical-ID information using an `OrderID` value.

Wire Finance can then use Microsoft Entra dynamic device-group rules to evaluate that value.

Conceptually:

```text
Group Tag
      ↓
[OrderID]:<VALUE>
      ↓
Dynamic Device Rule
      ↓
Device Group
      ↓
Provisioning Path
```

This matters because many operational properties do not yet exist before deployment.

The device may not yet be:

- Intune managed
- compliant
- fully configured
- assigned its final applications
- ready for normal use

But the provisioning service still needs to know what kind of endpoint it is supposed to build.

The Autopilot classification provides that early signal.

### `CORPORATE` Classification

A normal Wire Finance employee endpoint receives the Group Tag:

```text
CORPORATE
```

That classification is represented as:

```text
[OrderID]:CORPORATE
```

Wire Finance uses:

`SG-DEVICES-AUTOPILOT-CORPORATE`

to identify this provisioning population.

The implemented dynamic membership rule is:

```text
(device.devicePhysicalIds -any (_ -eq "[OrderID]:CORPORATE"))
```

The resulting relationship is:

```text
CORPORATE
      ↓
[OrderID]:CORPORATE
      ↓
SG-DEVICES-AUTOPILOT-CORPORATE
      ↓
Standard Corporate Provisioning
```

The important word is **intent**.

`CORPORATE` means:

**this device should receive the standard Wire Finance corporate build.**

It does not prove that the build has completed successfully.

### `PAW` Classification

Privileged Access Workstations require a different provisioning path.

They receive the Group Tag:

```text
PAW
```

The classification appears as:

```text
[OrderID]:PAW
```

Wire Finance uses:

`SG-DEVICES-PRIVILEGED`

to identify the privileged workstation population.

The tenant-validated dynamic membership rule is:

```text
(device.devicePhysicalIds -any (_ -contains "[OrderID]:PAW"))
```

The resulting relationship is:

```text
PAW
      ↓
[OrderID]:PAW
      ↓
SG-DEVICES-PRIVILEGED
      ↓
Privileged Workstation Provisioning
```

This allows Wire Finance to identify a privileged workstation before it becomes a normal operational endpoint.

A PAW may eventually use the same core cloud platforms as a standard endpoint.

But its intended security function is different.

### Why Classification Happens Before Provisioning

Imagine two physically similar Windows laptops arrive at Wire Finance.

The first will be used by Olivia Carter.

```text
Device A
      ↓
Purpose = Standard Employee Endpoint
      ↓
CORPORATE
      ↓
Standard Corporate Build
```

The second will be used for privileged administration.

```text
Device B
      ↓
Purpose = Privileged Administration
      ↓
PAW
      ↓
Privileged Workstation Build
```

The hardware may be similar.

The security requirements are not.

Without classification:

```text
New Device
      ↓
Which Build?
      ↓
Unknown
```

With classification:

```text
New Device
      ↓
Purpose Confirmed
      ↓
Group Tag
      ↓
Correct Device Group
      ↓
Correct Provisioning Path
```

Classification therefore sits between **business purpose** and **technical provisioning**.

Wire Finance decides what the device is for.

The technology then implements that decision.

### Classification Is Not Ownership

A Group Tag should not be confused with device ownership.

For example:

```text
Group Tag = CORPORATE
```

means:

**the device has been classified for the standard Wire Finance provisioning path.**

It does not independently prove:

- company ownership
- successful Intune enrollment
- compliance
- security health
- operational readiness
- trust

Those states are established through other parts of the device model.

The distinction is:

```text
AUTOPILOT CLASSIFICATION

"What should we build?"
```

versus:

```text
OPERATIONAL STATE

"What has actually been built,
and does it meet our requirements?"
```

A Group Tag is an input into provisioning.

It is not a certificate of trust.

### Classification Is Security-Sensitive Metadata

A Group Tag may look like a simple label:

```text
CORPORATE

PAW
```

but the value can influence what happens next.

```text
Group Tag
      ↓
Dynamic Group Membership
      ↓
Provisioning Target
      ↓
Configuration
      ↓
Security Posture
```

That makes Autopilot classification **security-sensitive provisioning metadata**.

The employee receiving the device should not decide which classification it receives.

A device intended for privileged administration should not enter that path merely because somebody renamed the computer or changed an informal label.

The classification must come from an authorised Wire Finance process.

### Classification Authority

Wire Finance defines who is allowed to make classification decisions.

| Action | Authority |
|---|---|
| **Register standard corporate device** | IT |
| **Assign `CORPORATE` tag** | IT |
| **Assign `PAW` tag** | IT with Security approval |
| **Change `CORPORATE` → `PAW`** | IT + Security |
| **Change `PAW` → `CORPORATE`** | IT + Security |
| **Remove Autopilot classification** | IT |
| **Validate PAW classification** | Security |
| **Review classification changes** | IT / Security |

The additional control around PAWs reflects their more sensitive purpose.

A normal employee cannot simply decide:

```text
I want this laptop to be a PAW.
```

Likewise, a privileged workstation should not be downgraded into an ordinary employee device without a controlled process.

### Standard Corporate Classification Workflow

For a standard employee endpoint, the expected classification path is:

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
Standard Corporate Provisioning
```

This connects the physical asset decision to the technical deployment path.

### Privileged Workstation Classification Workflow

A PAW requires stronger governance.

```text
Privileged Workstation Required
      ↓
Administrative Use Case Confirmed
      ↓
Security Approval
      ↓
Registered with Windows Autopilot
      ↓
PAW Tag Assigned
      ↓
SG-DEVICES-PRIVILEGED
      ↓
Privileged Workstation Provisioning
```

The stronger provisioning path exists because an approved privileged use case exists behind it.

The detailed PAW build is covered later in the Device Provisioning Model.

### What Happens If the Wrong Classification Is Applied?

Classification mistakes matter because the wrong Group Tag can place a device into the wrong provisioning population.

Imagine a device intended to become:

```text
WF-PAW-001
```

is accidentally given:

```text
CORPORATE
```

instead of:

```text
PAW
```

The hostname may still suggest that the device is a privileged workstation.

But:

**a device name does not create the security state.**

The device may have entered the standard corporate provisioning path instead of the privileged path.

Wire Finance therefore treats a material classification error as a provisioning problem.

```text
Misclassification Detected
        ↓
Stop Deployment or Privileged Use
        ↓
Confirm Correct Device Purpose
        ↓
Correct Autopilot Classification
        ↓
Verify Dynamic Group Membership
        ↓
Reset / Reprovision If Required
        ↓
Validate Correct Security State
        ↓
Return Device to Service
```

For a PAW in particular, the safest assumption is:

**if the device received the wrong security build, return it to a known-good state before privileged use.**

### Reclassification Is a Change of Purpose

Changing:

```text
CORPORATE
      ↓
PAW
```

is not simply changing a text value.

The existing endpoint was built according to one provisioning purpose and is now being assigned another.

The expected process is therefore:

```text
Change of Device Purpose
        ↓
Approval
        ↓
Update Autopilot Classification
        ↓
Verify Expected Group Membership
        ↓
Reset / Reprovision
        ↓
Correct Provisioning Path Applied
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

should not mean handing the existing privileged workstation directly to an employee for everyday productivity.

A controlled rebuild prevents privileged workstation state from silently following the hardware into its new purpose.

### Classification Values Must Stay Predictable

Wire Finance currently uses two approved uppercase Group Tag values:

```text
CORPORATE
PAW
```

The organisation deliberately avoids inconsistent alternatives such as:

```text
Corp
corporate
CorporateDevice
AdminLaptop
PAW2
VIP
IT
```

Uncontrolled values make automation harder to understand and govern.

A new Group Tag should therefore be introduced only when there is a **genuinely different foundational provisioning path**.

### Why There Is No `FINANCE`, `HR`, or `SALES` Tag

Autopilot classification is not intended to reproduce the organisational chart.

Finance, HR, Operations, Sales, and IT employees can all use the same underlying standard corporate endpoint architecture.

For example:

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

Department-specific applications, information access, and other user requirements can be targeted through the appropriate identity and application mechanisms.

The Autopilot classification remains focused on one question:

**Does this device require a materially different foundational provisioning path?**

That keeps the classification model simple.

### Why Pilot and Test Are Not Autopilot Classifications

Wire Finance also uses groups such as:

`SG-DEVICES-WINDOWS-PILOT`

and:

`SG-DEVICES-NONCOMPLIANT-TEST`

Those groups serve different purposes.

`PILOT` describes how a device is being used during a controlled rollout.

`NONCOMPLIANT TEST` describes a temporary testing state.

Neither necessarily requires a completely different foundational Windows build.

A test device might therefore follow the normal corporate Autopilot path while also being deliberately added to testing populations.

Conceptually:

```text
WF-TST-001
      ↓
Autopilot Tag = CORPORATE
      ↓
Standard Corporate Base Build
      ↓
Additional Test Membership
      ├── SG-DEVICES-WINDOWS-PILOT
      └── SG-DEVICES-NONCOMPLIANT-TEST
```

This is cleaner than creating a new Autopilot classification for every lab or rollout activity.

### Autopilot Classification and Device Groups

The architecture now connects cleanly.

```text
Device Purpose
      ↓
Autopilot Group Tag
      ↓
Dynamic Device Group
      ↓
Provisioning Target
      ↓
Provisioned Device
```

For the standard path:

```text
CORPORATE
      ↓
SG-DEVICES-AUTOPILOT-CORPORATE
      ↓
Standard Corporate Provisioning
```

For the privileged path:

```text
PAW
      ↓
SG-DEVICES-PRIVILEGED
      ↓
Privileged Workstation Provisioning
```

The Group Tag establishes provisioning classification.

The dynamic group converts that classification into a targetable population.

The provisioning configuration can then act on that population.

### Pre-Provisioning Validation

Before an important device begins provisioning, Wire Finance should be able to confirm its expected classification.

| Check | Standard Endpoint | PAW |
|---|---|---|
| **Device registered with Autopilot** | Yes | Yes |
| **Asset ownership confirmed** | Yes | Yes |
| **Intended purpose confirmed** | Employee endpoint | Privileged administration |
| **Group Tag** | `CORPORATE` | `PAW` |
| **Expected dynamic group** | `SG-DEVICES-AUTOPILOT-CORPORATE` | `SG-DEVICES-PRIVILEGED` |
| **Expected provisioning path** | Standard | Privileged |
| **Security approval** | Normal IT process | Required |

The objective is simple:

**catch a classification problem before it becomes a provisioning problem.**

### Wire Finance Autopilot Classification Standard

The current standard is:

```text
STANDARD CORPORATE DEVICE

Group Tag:
CORPORATE

OrderID:
[OrderID]:CORPORATE

Dynamic Group:
SG-DEVICES-AUTOPILOT-CORPORATE

Dynamic Rule:
(device.devicePhysicalIds -any (_ -eq "[OrderID]:CORPORATE"))

Purpose:
Standard Wire Finance Windows provisioning

Authority:
IT
```

and:

```text
PRIVILEGED ACCESS WORKSTATION

Group Tag:
PAW

OrderID:
[OrderID]:PAW

Dynamic Group:
SG-DEVICES-PRIVILEGED

Dynamic Rule:
(device.devicePhysicalIds -any (_ -contains "[OrderID]:PAW"))

Purpose:
Privileged workstation provisioning

Authority:
IT + Security
```

The governing principle is:

**Autopilot classification determines provisioning intent; it does not independently establish device trust.**

If the classification changes the device's security purpose, Wire Finance treats that as a controlled reprovisioning event rather than a simple label change.

### We Know the Build — Now We Need the Journey

Wire Finance can now identify the provisioning path a new corporate endpoint should follow.

```text
Corporate Asset
      ↓
Autopilot Registered
      ↓
CORPORATE
      ↓
SG-DEVICES-AUTOPILOT-CORPORATE
      ↓
Standard Corporate Provisioning
```

We know **which build** the standard device should receive.

The next question is what happens from beginning to end.

When is the device registered?

When does the employee sign in?

When does Microsoft Entra Join occur?

When does Intune enrollment happen?

When are applications and security controls delivered?

And when is the device finally considered ready for normal employee use?

That takes us to the **Standard Corporate Provisioning Journey**.