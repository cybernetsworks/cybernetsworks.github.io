---
layout: lesson
title: "Device Group Design"
series: wire-finance
parent: device-identity-provisioning
order: 4
description: "A single device may need several classifications at once. Wire Finance uses device groups to separate provisioning, operational management, privileged endpoints, staged rollouts, and controlled security testing."
---

We now know how Wire Finance devices are classified.

We know how they are named.

And we know that the first corporate Windows endpoints will use Microsoft Entra Join for device identity and Microsoft Intune for management.

But once those devices begin appearing inside the environment, another question appears:

**How do we make sure the right devices receive the right configuration?**

A standard employee laptop should not necessarily receive the same controls as a Privileged Access Workstation.

A test machine needs somewhere to receive experimental policies without making the production fleet our test environment.

And a device waiting for Windows Autopilot provisioning needs to be identifiable before it has completed the normal management journey.

Wire Finance solves this through **device groups**.

### What Device Groups Actually Do

Wire Finance uses Microsoft Entra security groups to classify and target devices according to different parts of their purpose or state.

Those classifications can later help determine which devices receive things such as:

- Windows Autopilot profiles
- Microsoft Intune configuration
- compliance policies
- endpoint-security controls
- application deployments
- staged pilot configurations
- privileged-device controls
- controlled test policies

The important principle is:

**one device can legitimately belong to several device groups at the same time.**

That is not duplication.

Each group answers a different question.

### One Device Can Have Several Classifications

Consider a corporate test endpoint.

That device might simultaneously be:

```text
Corporate
    +
Windows
    +
Intune Managed
    +
Selected for a Pilot
    +
Approved for a Controlled Test
```

Those characteristics describe different things.

Conceptually:

```text
One Device
    │
    ├── How should it be provisioned?
    │
    ├── Is it an operational corporate endpoint?
    │
    ├── Should it receive pilot configurations?
    │
    ├── Does it belong to the privileged tier?
    │
    └── Is it approved for controlled failure testing?
```

A single enormous group should not be expected to answer every one of those questions.

Wire Finance instead creates groups with clearly separated purposes.

### Dynamic and Assigned Membership

Not every device group should be populated in the same way.

Wire Finance uses two membership approaches.

#### Dynamic Device Membership

A **Dynamic Device** group calculates membership using device attributes.

Conceptually:

```text
Device Attributes
        ↓
Dynamic Rule
        ↓
Does the Device Match?
       / \
     Yes  No
      │    │
      ▼    ▼
   Member  Not Member
```

This works well when the classification can be derived from an authoritative technical signal.

Examples include:

- Windows operating system
- company ownership
- Intune management
- enabled device state
- Windows Autopilot classification

Dynamic membership reduces the need for administrators to maintain predictable technical classifications manually.

#### Assigned Membership

An **Assigned** group requires an administrator to deliberately select the device.

This is appropriate when membership represents a decision rather than something that should be inferred automatically.

Examples include:

- selecting a device for a controlled pilot
- approving a device for disruptive compliance testing

The design principle is:

```text
Can membership be determined
from an authoritative device signal?
             │
        ┌────┴────┐
        │         │
       Yes        No
        │         │
        ▼         ▼
     Dynamic    Assigned
```

Automation is used when the relationship is predictable.

Explicit assignment is used when someone needs to make a deliberate decision.

### Wire Finance Device Group Architecture

The initial Wire Finance endpoint model contains five device groups.

| Group | Membership | Primary Purpose |
|---|---|---|
| `SG-DEVICES-AUTOPILOT-CORPORATE` | Dynamic Device | Identify devices intended for standard corporate Autopilot provisioning |
| `SG-DEVICES-WINDOWS-CORPORATE` | Dynamic Device | Identify active corporate Windows devices under Intune management |
| `SG-DEVICES-WINDOWS-PILOT` | Assigned | Target selected devices for controlled endpoint rollouts |
| `SG-DEVICES-PRIVILEGED` | Dynamic Device | Identify devices classified for the privileged-device tier |
| `SG-DEVICES-NONCOMPLIANT-TEST` | Assigned | Identify devices deliberately selected for controlled compliance testing |

All five are Microsoft Entra **Security** groups.

They are used to classify and target devices.

They are not Microsoft Entra role-assignable groups.

### Provisioning Intent and Operational State

The most important distinction in the group design is between:

**what a device is intended to become**

and:

**what the device has become operationally.**

These are not the same thing.

#### Provisioning Intent

`SG-DEVICES-AUTOPILOT-CORPORATE`

answers:

**How should this device be provisioned?**

Wire Finance uses the Autopilot classification:

`CORPORATE`

A corporate Autopilot device exposes that classification through its physical ID information.

The dynamic group rule is:

```text
(device.devicePhysicalIds -any (_ -eq "[OrderID]:CORPORATE"))
```

Conceptually:

```text
Device Registered With Autopilot
        ↓
Group Tag = CORPORATE
        ↓
[OrderID]:CORPORATE
        ↓
SG-DEVICES-AUTOPILOT-CORPORATE
        ↓
Standard Corporate Provisioning
```

At this point, the device may not yet have completed Microsoft Entra Join or Microsoft Intune enrollment.

The group represents **provisioning intent**.

The detailed meaning and security implications of the `CORPORATE` Group Tag are covered in the next part of the Device Provisioning Model.

### Operational Corporate Windows Devices

Once a device has completed provisioning and management has been established, Wire Finance needs to answer a different question:

**Is this now an operational corporate Windows endpoint?**

That is the purpose of:

`SG-DEVICES-WINDOWS-CORPORATE`

Wire Finance uses the following dynamic rule:

```text
(device.deviceOSType -eq "Windows")
-and (device.deviceOwnership -eq "Company")
-and (device.deviceManagementAppId -eq "0000000a-0000-0000-c000-000000000000")
-and (device.accountEnabled -eq true)
```

The rule combines four conditions:

```text
Windows
    +
Company Owned
    +
Intune Managed
    +
Enabled
    ↓
SG-DEVICES-WINDOWS-CORPORATE
```

Each condition answers something different.

`device.deviceOSType`

confirms that the device is running Windows.

`device.deviceOwnership`

confirms that the device is classified as company-owned.

`device.deviceManagementAppId`

identifies the Microsoft Intune management relationship used by the Wire Finance design.

`device.accountEnabled`

ensures the device object remains enabled.

Together, those signals define the main operational corporate Windows population.

### Why the Two Corporate Groups Are Different

It would be easy to look at:

`SG-DEVICES-AUTOPILOT-CORPORATE`

and:

`SG-DEVICES-WINDOWS-CORPORATE`

and assume one should replace the other.

They answer different questions.

```text
SG-DEVICES-AUTOPILOT-CORPORATE
            ↓
     PROVISIONING INTENT

How should this device be built?
```

while:

```text
SG-DEVICES-WINDOWS-CORPORATE
            ↓
      OPERATIONAL STATE

What is this device now?
```

A device may legitimately remain in both.

```text
Provisioning Classification
          +
Operational Classification
          ↓
Both Can Be Valid
```

What matters is that each group is used for assignments appropriate to its purpose.

### Controlled Pilot Group

Not every new endpoint configuration should immediately target the production population.

Wire Finance therefore uses:

`SG-DEVICES-WINDOWS-PILOT`

Membership type:

`Assigned`

Pilot membership is deliberate because participation represents an IT decision.

Conceptually:

```text
Corporate Endpoint
        ↓
Selected for Controlled Rollout
        ↓
SG-DEVICES-WINDOWS-PILOT
        ↓
New Configuration
        ↓
Observe and Validate
        ↓
Wider Deployment
```

This gives Wire Finance a smaller population on which to validate new endpoint changes before targeting:

`SG-DEVICES-WINDOWS-CORPORATE`

The detailed pilot strategy is covered later in the **Pilot and Test Model**.

### User Pilots and Device Pilots Are Different

Earlier in the identity design, Wire Finance created:

`SG-PILOT-IDENTITY`

That is a **user group**.

It answers:

**Which users are participating in a controlled identity or enrollment pilot?**

The device model uses:

`SG-DEVICES-WINDOWS-PILOT`

That is a **device group**.

It answers:

**Which devices should receive pilot endpoint configurations?**

Conceptually:

```text
SG-PILOT-IDENTITY
        ↓
WHO participates?
```

while:

```text
SG-DEVICES-WINDOWS-PILOT
        ↓
WHICH DEVICES receive pilot policies?
```

The populations may be related during testing, but they are not interchangeable.

This distinction becomes important because Microsoft Intune can target configurations to users and devices independently.

### Privileged Device Group

Privileged Access Workstations need their own classification.

Wire Finance uses:

`SG-DEVICES-PRIVILEGED`

Membership type:

`Dynamic Device`

The provisioning classification is:

`PAW`

The group rule is:

```text
(device.devicePhysicalIds -any (_ -contains "[OrderID]:PAW"))
```

Conceptually:

```text
Device Classified as PAW
        ↓
Autopilot Group Tag = PAW
        ↓
SG-DEVICES-PRIVILEGED
        ↓
Privileged Device Path
```

This gives Wire Finance a dedicated population for endpoints intended to receive privileged-device controls.

The detailed PAW provisioning and security model is covered later.

### A PAW Can Also Be an Operational Corporate Device

Overlapping membership is expected here too.

After provisioning, a PAW may also satisfy the rule for:

`SG-DEVICES-WINDOWS-CORPORATE`

So a device may be:

```text
WF-PAW-001
    │
    ├── SG-DEVICES-WINDOWS-CORPORATE
    │       ↓
    │   Operational corporate Windows endpoint
    │
    └── SG-DEVICES-PRIVILEGED
            ↓
        Privileged device classification
```

The memberships do not conflict.

One describes its broad operational state.

The other describes its privileged security purpose.

This reinforces another useful principle:

**same operating platform does not mean same security requirements.**

### Controlled Noncompliance Test Group

Wire Finance also needs a way to deliberately test what happens when a device fails a compliance requirement.

That is the purpose of:

`SG-DEVICES-NONCOMPLIANT-TEST`

Membership type:

`Assigned`

The group does **not** mean:

```text
Put every noncompliant device here
```

There is an important distinction:

```text
Device Becomes Noncompliant
        ≠
Device Is Approved for
Intentional Noncompliance Testing
```

A production device that becomes noncompliant represents an operational or security problem.

A device placed into:

`SG-DEVICES-NONCOMPLIANT-TEST`

has been deliberately selected for a controlled test.

Assigned membership is therefore appropriate because intentionally introducing failure should require a conscious decision.

The detailed test workflow belongs in the **Pilot and Test Model**.

### Matching Groups to Their Jobs

The group architecture can now be summarised clearly.

| Requirement | Preferred Group |
|---|---|
| **Standard Autopilot provisioning** | `SG-DEVICES-AUTOPILOT-CORPORATE` |
| **Normal corporate Windows management** | `SG-DEVICES-WINDOWS-CORPORATE` |
| **Controlled endpoint rollout** | `SG-DEVICES-WINDOWS-PILOT` |
| **Privileged-device classification** | `SG-DEVICES-PRIVILEGED` |
| **Controlled compliance testing** | `SG-DEVICES-NONCOMPLIANT-TEST` |

Conceptually:

```text
Provisioning
      ↓
Provisioning Group

Normal Operations
      ↓
Operational Group

Controlled Rollout
      ↓
Pilot Group

Privileged Device
      ↓
Privileged Group

Controlled Failure Testing
      ↓
Test Group
```

Each group has one primary responsibility.

That is preferable to building one enormous device group that attempts to describe every stage and purpose of a device.

### Overlapping Membership Is Expected

A device may accumulate several valid classifications as it moves through its lifecycle.

For example, a normal test endpoint might eventually be:

```text
SG-DEVICES-WINDOWS-CORPORATE
        +
SG-DEVICES-WINDOWS-PILOT
        +
SG-DEVICES-NONCOMPLIANT-TEST
```

There is no contradiction.

Each membership answers a separate question.

| Classification | Question Being Answered |
|---|---|
| **Autopilot Corporate** | How should this device be provisioned? |
| **Windows Corporate** | Is this an active managed corporate Windows endpoint? |
| **Windows Pilot** | Should this device receive pilot configurations? |
| **Privileged** | Does this device belong to the privileged-device class? |
| **Noncompliant Test** | Is this device approved for controlled failure testing? |

The core design principle is therefore:

**classify devices by purpose and state instead of trying to make one group describe everything.**

### Group Membership Is Not Device Trust

Groups make targeting easier.

They do not replace the trust model.

For example, membership in:

`SG-DEVICES-WINDOWS-CORPORATE`

tells us useful things about the device's operating and management state.

But it should not be interpreted as:

```text
Member of Corporate Group
        =
Automatically Trusted Forever
```

Device trust will eventually depend on multiple signals, including management, security configuration, compliance, and risk.

A group helps Wire Finance organise devices and target controls.

It should not become a shortcut for assuming that every security requirement has been satisfied.

### Current Device Group Record

The Wire Finance device-group design is therefore:

| Group | Type | Membership | Classification Source |
|---|---|---|---|
| `SG-DEVICES-AUTOPILOT-CORPORATE` | Security | Dynamic Device | Autopilot `CORPORATE` classification |
| `SG-DEVICES-WINDOWS-CORPORATE` | Security | Dynamic Device | Windows + Company ownership + Intune management + enabled state |
| `SG-DEVICES-WINDOWS-PILOT` | Security | Assigned | Explicit IT selection |
| `SG-DEVICES-PRIVILEGED` | Security | Dynamic Device | Autopilot `PAW` classification |
| `SG-DEVICES-NONCOMPLIANT-TEST` | Security | Assigned | Explicit IT / Security selection |

That gives Wire Finance:

```text
3 Dynamic Device Groups
        +
2 Assigned Device Groups
        =
5 Device Security Groups
```

The membership model reflects the nature of each decision.

```text
Authoritative Technical Signal
            ↓
      Dynamic Device

Deliberate Human Decision
            ↓
          Assigned
```

### The Groups Are Ready — Now We Need to Understand the Classification

Two of the device groups depend on values that deserve closer attention:

```text
CORPORATE
PAW
```

Those values are Windows Autopilot **Group Tags**.

They help determine which provisioning path a device should enter.

That also makes their assignment security-relevant.

A normal device incorrectly classified as `PAW` could enter the wrong provisioning path.

A privileged device incorrectly classified as `CORPORATE` could miss controls intended for the privileged tier.

So the next question becomes:

**What exactly is an Autopilot Group Tag, who should be allowed to assign one, and how does Wire Finance use it to separate corporate and privileged provisioning?**