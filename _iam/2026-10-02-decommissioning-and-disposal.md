---
layout: lesson
title: "Decommissioning and Disposal"
series: wire-finance
parent: device-lifecycle
order: 10
description: "Retirement says the device should leave. Decommissioning makes it happen by reconciling Intune, Entra ID, Defender, Autopilot, group membership, business records, and the physical asset."
---

Retirement is the decision.

Decommissioning is the cleanup.

The distinction is:

```text
RETIREMENT
"This Device Is Leaving Active Service"
        ↓
DECOMMISSIONING
"Remove Its Remaining Organisational Relationships"
        ↓
DISPOSAL
"Complete the Physical Lifecycle"
```

A device is not fully offboarded simply because it has disappeared from one Microsoft portal.

#### The Permanent Departure Flow

For a device permanently leaving Wire Finance:

```text
Retirement Approved
        ↓
Required Business Data Preserved
        ↓
Secure Reset / Wipe
        ↓
Remove Intune Relationship
Where Appropriate
        ↓
Remove Entra Device Identity
Where Appropriate
        ↓
Validate Defender Offboarding
        ↓
Remove Windows Autopilot Registration
        ↓
Review Device Group Membership
        ↓
Update Asset Register
        ↓
Dispose / Transfer Physical Asset
```

Every stage closes a different part of the device's relationship with Wire Finance.

#### Why Autopilot Removal Matters

Autopilot represents the provisioning relationship between the hardware and Wire Finance.

If the device remains an internal corporate asset, that relationship may still be required.

If the hardware permanently leaves Wire Finance:

```text
Device No Longer Belongs
to Wire Finance Provisioning
```

the Autopilot relationship needs to be removed.

Otherwise, the hardware may continue behaving as though Wire Finance still owns its provisioning experience.

#### Decommissioning Checklist

| Record / Control | Final Action |
|---|---|
| **Employee assignment** | Remove or transfer the assignment |
| **Business data** | Preserve required records and securely remove residual data |
| **Microsoft Intune** | Remove management relationship / record as appropriate |
| **Microsoft Entra ID** | Remove device identity where appropriate |
| **Microsoft Defender for Endpoint** | Validate security offboarding and expected record retention |
| **Windows Autopilot** | Remove registration when the hardware permanently leaves Wire Finance |
| **Assigned device groups** | Explicitly review Pilot/Test membership and other manual assignments |
| **Asset Register** | Record retirement and disposal outcome while retaining history |
| **Physical asset** | Return, recycle, sell, transfer, or dispose according to approved policy |

This is why:

```text
Removed From Intune
```

does not mean:

```text
Fully Offboarded
```

Other organisational relationships can still exist.

#### Device Group Cleanup

During its life, a device might have belonged to groups such as:

```text
SG-DEVICES-AUTOPILOT-CORPORATE

SG-DEVICES-WINDOWS-CORPORATE

SG-DEVICES-WINDOWS-PILOT

SG-DEVICES-NONCOMPLIANT-TEST

SG-DEVICES-PRIVILEGED
```

Dynamic and assigned groups behave differently during cleanup.

```text
Dynamic Membership
        ↓
State / Attribute Driven
        ↓
Membership Changes as
Underlying State Changes
```

while:

```text
Assigned Membership
        ↓
Explicit Decision
        ↓
Explicit Cleanup Required
```

Temporary groups such as:

`SG-DEVICES-WINDOWS-PILOT`

and:

`SG-DEVICES-NONCOMPLIANT-TEST`

therefore need deliberate review.

#### Keep the Historical Asset Record

When the physical lifecycle completes:

```text
Lifecycle Status:
Disposed
```

the active endpoint may disappear from management platforms.

The historical Asset Register record should not simply vanish.

Wire Finance may still need to establish:

```text
What Was the Device?

Who Was It Assigned To?

What Was Its Purpose?

When Did It Leave Service?

Why Was It Retired?

How Was It Disposed?
```

Operational records and historical accountability serve different purposes.

#### Final Lifecycle States

The final progression is:

```text
Retirement Pending
        ↓
Cleanup In Progress
        ↓
Retired
        ↓
No Longer Operational
        ↓
Disposed
        ↓
Physical Lifecycle Complete
```

##### Retirement Pending

The business decision has been made, but cleanup remains incomplete.

##### Retired

The device is no longer an operational Wire Finance endpoint.

##### Disposed

The physical lifecycle has also been completed.

#### The Device Lifecycle and User Lifecycle

The device journey now creates a useful bridge into the identity lifecycle.

| User Lifecycle | Comparable Device Lifecycle |
|---|---|
| **Joiner** | Acquire → Provision → Assign |
| **Mover** | Reassign → Repurpose → Revalidate |
| **Leaver** | Recover → Reset → Reassign **or** Retire → Decommission |

For example:

```text
USER

Olivia Leaves
      ↓
Identity Disabled
      ↓
Access Removed
```

does not automatically mean:

```text
DEVICE

WF-LT-001
      ↓
Disposed
```

Instead:

```text
WF-LT-001 Recovered
      ↓
Still Useful?
   /         \
 Yes          No
  ↓            ↓
Reset        Retire
  ↓            ↓
Reassign     Decommission
```

The user and device lifecycles interact.

They remain separate governance processes.

#### Wire Finance Device Lifecycle Decision

The complete lifecycle can now be recorded as:

```text
Lifecycle Authority:
Wire Finance IT / Endpoint Management

Business Asset Record:
Wire Finance Asset Register


Entry Into Service:
Acquire
   ↓
Register
   ↓
Provision
   ↓
Validate


Operational Lifecycle:
In Service
   ↓
Maintain
   ↓
Repair / Reassign / Security Hold
   ↓
Return to Service Where Appropriate


Internal Reuse:
Controlled Reset / Reprovisioning
        ↓
Revalidate
        ↓
Reassign


Retirement:
Business Decision to Remove
the Device from Active Service


Decommissioning:
Coordinated Cleanup Across:

Wire Finance Asset Register
Microsoft Intune
Microsoft Entra ID
Microsoft Defender for Endpoint
Windows Autopilot
Device Groups
Physical Asset


Autopilot Removal:
Required when registered hardware
permanently leaves Wire Finance


Final States:
Retirement Pending
        ↓
Retired
        ↓
Disposed
```

The final principle is:

**a device is not fully offboarded until business ownership, cloud identity and management, provisioning records, security state, group assignments, and the physical asset have all been reconciled.**

#### The Device Identity and Provisioning Model Is Complete

We started with:

```text
Physical Hardware
```

and progressively established:

```text
Ownership
   ↓
Naming
   ↓
Device Identity
   ↓
Provisioning
   ↓
Management
   ↓
Security
   ↓
Compliance
   ↓
Operation
   ↓
Lifecycle Management
   ↓
Retirement
   ↓
Decommissioning
```

Wire Finance now has a defined device journey from acquisition to permanent disposal.

But devices are only one half of the organisation.

The people using them also have a lifecycle.

They join.

They change roles.

Their access requirements change.

And eventually, they leave.

**So what should happen to an identity as a person moves through Wire Finance?**