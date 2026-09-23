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

And we know that new corporate Windows endpoints will use Microsoft Entra Join for identity and Microsoft Intune for management.

But once those devices begin appearing inside the environment, another question appears:

**How do we make sure the right devices receive the right configuration?**

A standard employee laptop should not receive the same controls as a Privileged Access Workstation.

A test machine should be able to receive experimental policies without affecting production endpoints.

And a device waiting for Windows Autopilot provisioning needs to be identifiable before it has completed the normal management journey.

Wire Finance solves this through **device groups**.

#### What Device Groups Actually Do

Wire Finance uses Microsoft Entra **Security groups** to classify devices according to their:

- Provisioning state
- Operational state
- Security purpose
- Testing role
- Deployment stage

These groups create targeting boundaries.

Later, they can help determine which devices receive:

- Autopilot profiles
- Configuration policies
- Compliance policies
- Endpoint security controls
- Application deployments
- Pilot configurations
- Privileged workstation controls
- Controlled test policies

But there is an important principle behind the design:

**one device can legitimately belong to several device groups at the same time.**

That is not duplication.

Each group answers a different question.

#### One Device, Several Questions

Consider:

`WF-TST-001`

Depending on how it is being used, that single device could belong to several groups:

```text
WF-TST-001
│
├── SG-DEVICES-AUTOPILOT-CORPORATE
│      ↓
│   How should this device be provisioned?
│
├── SG-DEVICES-WINDOWS-CORPORATE
│      ↓
│   Is this an operational corporate Windows endpoint?
│
├── SG-DEVICES-WINDOWS-PILOT
│      ↓
│   Should this device receive pilot configurations?
│
└── SG-DEVICES-NONCOMPLIANT-TEST
       ↓
    Is this device approved for intentional
    compliance-failure testing?
```

The memberships overlap because the questions are different.

A single group should not be expected to describe everything about a device.

#### Dynamic and Assigned Membership

Wire Finance uses two membership approaches for device groups.

##### Dynamic Device Membership

A **Dynamic Device** group calculates membership from device attributes.

Conceptually:

```text
Device Attributes
      ↓
Dynamic Rule
      ↓
Rule Matches?
   /        \
 Yes        No
  ↓          ↓
Member    Not Member
```

This works well where membership can be determined from an authoritative technical signal.

Examples include:

```text
Autopilot Classification
Windows Operating System
Corporate Ownership
Intune Management
Enabled Device State
```

If the device attributes satisfy the rule, Microsoft Entra can calculate the membership automatically.

##### Assigned Membership

An **Assigned** device group requires an administrator to deliberately choose the device.

That makes sense when membership represents a decision rather than a property that should be inferred automatically.

Examples include:

```text
Selected for Pilot
Approved for Disruptive Testing
```

So the design follows a familiar principle:

```text
Can membership be determined
from an authoritative device signal?
             │
         Yes │ No
             │
        ┌────┴────┐
        ↓         ↓
     Dynamic    Assigned
      Device
```

Automation is used where the classification is predictable.

Explicit assignment is used where somebody needs to make a deliberate decision.

#### Device Group Architecture

At the current design checkpoint, Wire Finance has created five device groups:

| Group | Membership | Primary Function | Lifecycle Stage |
|---|---|---|---|
| `SG-DEVICES-AUTOPILOT-CORPORATE` | Dynamic Device | Standard corporate Autopilot provisioning | Before / during provisioning |
| `SG-DEVICES-WINDOWS-CORPORATE` | Dynamic Device | Operational corporate Windows population | Operational / steady state |
| `SG-DEVICES-WINDOWS-PILOT` | Assigned | Controlled endpoint-policy testing | Pilot / staged rollout |
| `SG-DEVICES-PRIVILEGED` | Dynamic Device | PAW provisioning and privileged-device targeting | Provisioning + operation |
| `SG-DEVICES-NONCOMPLIANT-TEST` | Assigned | Controlled compliance and failure testing | Lab / controlled testing |

All five are:

```text
Group Type:
Security

Microsoft Entra Roles Assignable:
No
```

Their job is to classify and target devices.

They are not being used to assign Microsoft Entra administrative roles.

---

#### Standard Corporate Autopilot Group

The first group answers:

**How should this new device be provisioned?**

Group:

`SG-DEVICES-AUTOPILOT-CORPORATE`

Membership:

`Dynamic Device`

Authoritative classification:

`Autopilot Group Tag = CORPORATE`

The dynamic rule is:

```text
(device.devicePhysicalIds -any (_ -eq "[OrderID]:CORPORATE"))
```

The relationship is:

```text
Device Registered with Autopilot
        ↓
Group Tag = CORPORATE
        ↓
OrderID Classification
        ↓
SG-DEVICES-AUTOPILOT-CORPORATE
        ↓
Standard Wire Finance
Autopilot Provisioning
```

This is principally a **provisioning group**.

It identifies devices that should follow the standard corporate Windows provisioning path.

At this stage, the device may not yet have completed Microsoft Entra Join or Intune enrollment.

That is why its provisioning classification needs to exist before the normal operational state has been established.

#### Operational Corporate Windows Group

Once a device has been provisioned and enrolled, we need to answer a different question:

**Is this now an active Wire Finance managed Windows endpoint?**

That is the purpose of:

`SG-DEVICES-WINDOWS-CORPORATE`

Membership:

`Dynamic Device`

The implemented rule is:

```text
(device.deviceOSType -eq "Windows")
-and (device.deviceOwnership -eq "Company")
-and (device.deviceManagementAppId -eq "0000000a-0000-0000-c000-000000000000")
-and (device.accountEnabled -eq true)
```

The rule checks four conditions:

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

Each condition contributes something different.

`device.deviceOSType`

confirms that the endpoint is Windows.

`device.deviceOwnership`

confirms that the ownership classification is Company.

`device.deviceManagementAppId`

identifies the management relationship used by the Wire Finance Intune model.

`device.accountEnabled`

ensures the device object remains enabled.

Together, those signals define the primary **operational corporate Windows population**.

#### Why This Group Is Different From the Autopilot Group

This distinction is one of the most important parts of the device design.

The Autopilot group answers:

```text
How should this device be built?
```

The corporate Windows group answers:

```text
What is this device now that it is operational?
```

So:

```text
SG-DEVICES-AUTOPILOT-CORPORATE
             ↓
      PROVISIONING INTENT
```

while:

```text
SG-DEVICES-WINDOWS-CORPORATE
             ↓
       OPERATIONAL STATE
```

The first group is not simply replaced by the second.

A device may remain a member of both because the classifications answer different questions.

```text
Provisioning Classification
          +
Operational Classification
          ↓
Both Can Be Valid
```

What matters is using the appropriate group for the appropriate assignment.

#### What the Operational Group Can Eventually Receive

Once a device belongs to the normal managed population, the group can eventually become a broad targeting point for production controls such as:

```text
Compliance Policies
Configuration Profiles
Windows Update Policies
Endpoint Security
Standard Applications
Microsoft Defender Controls
```

That makes:

`SG-DEVICES-WINDOWS-CORPORATE`

one of the main steady-state management groups in the endpoint architecture.

#### Controlled Pilot Group

Not every change should immediately target the entire production fleet.

That is why Wire Finance created:

`SG-DEVICES-WINDOWS-PILOT`

Membership:

`Assigned`

Pilot membership is deliberately manual.

Being selected for a pilot is an **IT decision**.

It should not happen simply because a technical device attribute happens to match something.

The journey becomes:

```text
Normal Corporate Endpoint
        ↓
Selected for Controlled Rollout
        ↓
SG-DEVICES-WINDOWS-PILOT
```

This provides a smaller device population for testing new configurations.

For example:

```text
New Endpoint Policy
        ↓
SG-DEVICES-WINDOWS-PILOT
        ↓
Validate Behaviour
        ↓
Identify Problems
        ↓
Correct Issues
        ↓
SG-DEVICES-WINDOWS-CORPORATE
```

The pilot group gives Wire Finance somewhere to test before production becomes the test environment.

#### User Pilot and Device Pilot Are Different

Earlier, we defined:

`SG-PILOT-IDENTITY`

That is a **user group**.

It answers:

**Which users participate in the initial identity and enrollment pilot?**

Now we also have:

`SG-DEVICES-WINDOWS-PILOT`

That is a **device group**.

It answers:

**Which devices should receive pilot endpoint configurations?**

So:

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

The two populations may be related during testing, but they are not interchangeable.

This becomes especially important when Intune begins targeting settings independently to users and devices.

#### Privileged Device Group

Privileged Access Workstations require their own classification.

Wire Finance created:

`SG-DEVICES-PRIVILEGED`

Membership:

`Dynamic Device`

Authoritative Autopilot classification:

`PAW`

The implemented rule is:

```text
(device.devicePhysicalIds -any (_ -contains "[OrderID]:PAW"))
```

The classification path becomes:

```text
Device Classified as PAW
        ↓
Autopilot Group Tag = PAW
        ↓
SG-DEVICES-PRIVILEGED
        ↓
PAW Provisioning
        ↓
Security Hardening
        ↓
Stricter Compliance
        ↓
Privileged Access Controls
```

This gives Wire Finance a dedicated device population for privileged endpoints.

#### A PAW Can Still Be a Corporate Windows Device

This is another example where overlapping membership is correct.

After provisioning, a PAW may satisfy the operational corporate Windows rule as well.

So:

```text
WF-PAW-001
│
├── SG-DEVICES-WINDOWS-CORPORATE
│      ↓
│   Operational corporate Windows device
│
└── SG-DEVICES-PRIVILEGED
       ↓
    Privileged security tier
```

Those memberships do not conflict.

One describes the device's **general management state**.

The other describes its **security tier**.

That gives us another useful principle:

**same operational platform does not mean same security requirements.**

#### Noncompliance Test Group

The final group exists for deliberate security testing.

`SG-DEVICES-NONCOMPLIANT-TEST`

Membership:

`Assigned`

It is deliberately assigned because intentionally breaking compliance should always be a conscious test decision.

The group does **not** mean:

```text
Put every noncompliant device here
```

There is an important difference:

```text
Device Is Noncompliant
        ≠
Device Is an Approved
Noncompliance Test Device
```

A production device may become noncompliant because something has gone wrong.

That is a security condition that needs investigation or remediation.

A test device is intentionally placed into:

`SG-DEVICES-NONCOMPLIANT-TEST`

because we want to observe what happens when a controlled failure occurs.

The test workflow becomes:

```text
Test Scenario Approved
        ↓
Dedicated Test Device Selected
        ↓
SG-DEVICES-NONCOMPLIANT-TEST
        ↓
Controlled Failure Introduced
        ↓
Intune Behaviour Observed
        ↓
Conditional Access Behaviour Observed
        ↓
SOC / Security Visibility Observed
        ↓
Configuration Restored
        ↓
Device Removed From Test Group
```

`WF-TST-001`

is the obvious first candidate for this type of exercise.

This protects ordinary production endpoints from receiving disruptive test configurations.

---

#### Provisioning Groups and Operational Groups

We can now separate the major responsibilities clearly.

| Requirement | Preferred Group |
|---|---|
| **Standard Autopilot deployment profile** | `SG-DEVICES-AUTOPILOT-CORPORATE` |
| **Standard production compliance** | `SG-DEVICES-WINDOWS-CORPORATE` |
| **Standard endpoint configuration** | `SG-DEVICES-WINDOWS-CORPORATE` |
| **Controlled new-policy rollout** | `SG-DEVICES-WINDOWS-PILOT` |
| **PAW provisioning / hardening** | `SG-DEVICES-PRIVILEGED` |
| **PAW compliance** | `SG-DEVICES-PRIVILEGED` |
| **Failure and compliance testing** | `SG-DEVICES-NONCOMPLIANT-TEST` |

This prevents us from building one enormous device group that tries to control every stage of the endpoint lifecycle.

Instead:

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

Privileged Security Tier
      ↓
Privileged Group

Controlled Failure Testing
      ↓
Test Group
```

Each group has a clear responsibility.

#### Device Group Lifecycle

The individual groups make more sense when we look at them as part of one device journey.

A new corporate device does not immediately appear in every group.

Its classifications develop as the device moves from **provisioning** into **normal operation**, and additional groups can then identify special purposes such as pilot deployment, privileged administration, or controlled testing.

![Wire Finance Device Group Lifecycle]({{ '/assets/images/device-management/device_group_lifecycle.png' | relative_url }})

The journey begins before the device is fully operational.

```text
New Device
      ↓
Autopilot Classification
      ↓
Provisioning Group
      ↓
Microsoft Entra Join + Intune Enrollment
      ↓
Operational Corporate Group
```

For a standard corporate endpoint, the relationship is:

```text
CORPORATE Group Tag
        ↓
SG-DEVICES-AUTOPILOT-CORPORATE
        ↓
Standard Autopilot Provisioning
        ↓
Microsoft Entra Join
        ↓
Intune Enrollment
        ↓
SG-DEVICES-WINDOWS-CORPORATE
```

At that point, the device has moved from **provisioning intent** into its normal **operational state**.

But the journey does not necessarily stop there.

An operational device may also receive another classification depending on what Wire Finance intends to do with it.

```text
Operational Corporate Device
        │
        ├── Pilot
        │     ↓
        │   SG-DEVICES-WINDOWS-PILOT
        │
        ├── Privileged
        │     ↓
        │   SG-DEVICES-PRIVILEGED
        │
        └── Controlled Test
              ↓
            SG-DEVICES-NONCOMPLIANT-TEST
```

This is why overlapping device-group membership is expected.

The groups are not competing to decide what the device **is**.

They are describing different aspects of the device.

| Classification | Question Being Answered |
|---|---|
| **Autopilot Corporate** | How should this device be provisioned? |
| **Windows Corporate** | Is this an active managed corporate Windows endpoint? |
| **Windows Pilot** | Should this device receive pilot configurations? |
| **Privileged** | Does this device belong to the privileged security tier? |
| **Noncompliant Test** | Is this device approved for controlled failure testing? |

For example, a Privileged Access Workstation can legitimately be:

```text
SG-DEVICES-PRIVILEGED
        +
SG-DEVICES-WINDOWS-CORPORATE
```

The first membership identifies its **security tier**.

The second identifies its **operational management state**.

Likewise, a test endpoint could temporarily belong to:

```text
SG-DEVICES-WINDOWS-CORPORATE
        +
SG-DEVICES-WINDOWS-PILOT
        +
SG-DEVICES-NONCOMPLIANT-TEST
```

without any contradiction.

Each membership answers a separate operational or security question.

That is the core idea behind the Wire Finance device-group architecture:

**classify devices by purpose and state instead of trying to make one group describe everything.**

#### Group Membership Does Not Equal Trust

Device groups make targeting easier.

They do not eliminate the trust model we established earlier.

For example:

```text
Member of
SG-DEVICES-WINDOWS-CORPORATE
```

is useful evidence about the device's operational state.

But Wire Finance still evaluates trust progressively.

```text
Approved Ownership
      ↓
Device Identity
      ↓
Management
      ↓
Security Controls
      ↓
Compliance
      ↓
Access Decisions
```

A group helps us organise and target devices.

It should not become a shortcut for assuming that every security requirement has been satisfied.

The same principle still applies:

**device presence does not equal device trust.**

---

#### Current Device Group Record

The current Wire Finance device group implementation is:

| Group | Type | Membership | Classification Source | Status |
|---|---|---|---|---|
| `SG-DEVICES-AUTOPILOT-CORPORATE` | Security | Dynamic Device | Autopilot `CORPORATE` Group Tag | Created |
| `SG-DEVICES-WINDOWS-CORPORATE` | Security | Dynamic Device | Windows + Company ownership + Intune management + enabled state | Created |
| `SG-DEVICES-WINDOWS-PILOT` | Security | Assigned | Explicit IT selection | Created |
| `SG-DEVICES-PRIVILEGED` | Security | Dynamic Device | Autopilot `PAW` classification | Created |
| `SG-DEVICES-NONCOMPLIANT-TEST` | Security | Assigned | Explicit IT / Security selection | Created |

That gives us:

```text
3 Dynamic Device Groups
        +
2 Assigned Device Groups
        =
5 Device Security Groups
```

The membership model reflects the kind of decision each group represents.

```text
Authoritative Technical Classification
              ↓
         Dynamic Device


Deliberate Human Selection
              ↓
            Assigned
```

#### A Note About the Autopilot Model

For this Wire Finance build, we are designing around the **Windows Autopilot deployment-profile model**.

The current design uses Autopilot Group Tags to classify devices before provisioning:

```text
CORPORATE
PAW
```

Those classifications can then drive dynamic device-group membership.

Microsoft also has a separate workflow called **Windows Autopilot device preparation**.

That is not the model being implemented here.

For Wire Finance, the current decision remains:

```text
Windows Autopilot
User-Driven Microsoft Entra Join
        +
Dynamic Group Tag Classification
```

Keeping that distinction clear prevents us from accidentally mixing configuration steps from two related but different provisioning approaches.

#### The Groups Are Ready — Now We Need to Understand the Tags

The device groups now give us structure.

We know which group represents:

```text
Provisioning Intent
Operational State
Pilot Deployment
Privileged Security Tier
Controlled Failure Testing
```

But two of those groups depend on something we have not yet explored properly:

```text
CORPORATE
PAW
```

Those values are not just convenient labels.

They influence which provisioning path a device can enter.

And that makes classification itself a security-sensitive decision.

So the next question is:

**What exactly is an Autopilot Group Tag, who should be allowed to assign one, and what happens if a normal device is accidentally classified as a PAW — or a PAW as a normal corporate endpoint?**