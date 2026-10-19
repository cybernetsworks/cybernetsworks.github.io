---
layout: lesson
title: "Device Lifecycle Overview"
series: wire-finance
parent: device-identity-provisioning
order: 10
nav_id: device-lifecycle
children_heading: "Explore the Device Lifecycle"
description: "Follow a Wire Finance device through its complete managed lifecycle, from acquisition and provisioning to reassignment, security response, retirement, and final disposal."
---

Up to this point, most of the Wire Finance device design has answered one question:

**How do we get a device safely into service?**

We have defined how devices are:

- owned
- named
- classified
- joined
- provisioned
- managed
- secured
- tested
- evaluated for compliance

But provisioning is only the beginning.

A corporate device may remain inside the organisation for years.

During that time it may be updated, repaired, reassigned, placed into a pilot, investigated after a security event, rebuilt, retired, or eventually disposed of.

The final part of the device model therefore asks:

**What happens to a device from the moment Wire Finance acquires it until the physical asset permanently leaves the organisation?**

The principle is simple:

**a managed device has a lifecycle, not just an enrollment event.**

### The Complete Device Journey

The Wire Finance device lifecycle is shown below.

![Wire Finance Device Lifecycle Overview]({{ '/assets/images/active-directory/device_lifecycle_overview.png' | relative_url }})

At a high level, the journey can be understood in three phases:

```text
ENTER SERVICE
      ↓
Acquire
      ↓
Register
      ↓
Provision
      ↓
Validate


OPERATE
      ↓
In Service
      ↓
Maintain
      ↓
Repair / Reassign / Security Response
      ↓
Return to Service Where Appropriate


LEAVE SERVICE
      ↓
Retirement Decision
      ↓
Decommission
      ↓
Dispose
```

The journey is not always a straight line.

A device may move through paths such as:

```text
In Service
    ↓
Repair
    ↓
In Service
```

or:

```text
In Service
    ↓
Reassignment
    ↓
Provision / Validate
    ↓
In Service
```

A security event may take the device down a different path:

```text
In Service
    ↓
Security Hold
    ↓
Investigate
   /           \
Recover       Retire
   ↓
Return to
Service
```

Lifecycle management therefore means controlling both the **normal journey** and the situations where a device leaves that normal path.

### One Device, Several Records

Consider:

`WF-LT-001`

That one physical device may eventually be represented across several systems:

```text
WF-LT-001
│
├── Wire Finance Asset Register
├── Windows Autopilot
├── Microsoft Entra ID
├── Microsoft Intune
└── Microsoft Defender for Endpoint
```

Those records are not duplicates.

Each answers a different question.

| Record | What It Tells Us |
|---|---|
| **Asset Register** | What physical asset does Wire Finance own, who has it, and what is its current business state? |
| **Windows Autopilot** | What provisioning relationship and intent has been assigned to the hardware? |
| **Microsoft Entra ID** | What is the device's organisational identity? |
| **Microsoft Intune** | Is the device managed, configured, and compliant? |
| **Microsoft Defender for Endpoint** | What security activity and risk are associated with the endpoint? |

The lifecycle process keeps these different views aligned.

A device may be physically returned but still have cloud records.

It may be removed from service while still existing in the asset register.

It may be reassigned to another employee while remaining the same physical asset.

That is why no single Microsoft portal represents the entire device lifecycle.

### Business State and Technical State Are Different

Wire Finance also separates the **business state of the physical asset** from its technical state inside Microsoft platforms.

For example:

```text
Asset Register
      ↓
In Service
Repair
Reassignment
Security Hold
Retirement Pending
Disposed
```

while Microsoft platforms may independently tell us things such as:

```text
Entra Joined
Intune Managed
Compliant
Non-Compliant
Defender Onboarded
At Risk
```

Those states are related, but they are not interchangeable.

A device marked `Security Hold` in the asset register may still be enrolled in Intune because Security needs to investigate it.

A device marked `Retirement Pending` may still have Entra, Intune, Defender, or Autopilot records that need to be cleaned up before the lifecycle is complete.

This is why the Asset Register remains part of the Wire Finance device model.

### Lifecycle Responsibility Changes Too

Responsibility follows the device through its lifecycle.

At different points, the device may involve:

- IT for acquisition, provisioning, assignment, repair, and retirement
- Security for privileged-device decisions and incident response
- Microsoft Intune for management and compliance
- Microsoft Defender for Endpoint for security monitoring and risk
- the business for user assignment, handover, and retirement decisions
- the Asset Register for the authoritative physical-asset record

No single portal — and no single technical control — owns the entire device story.

The lifecycle works because those responsibilities remain coordinated.

### From Provisioning to Lifecycle Management

Wire Finance can now describe the complete device journey:

```text
Acquire
   ↓
Record
   ↓
Classify
   ↓
Provision
   ↓
Secure
   ↓
Validate
   ↓
Operate
   ↓
Maintain / Reassign / Investigate
   ↓
Retire
   ↓
Decommission
   ↓
Dispose
```

The important point is that provisioning creates the beginning of a managed device.

It does not create the end state.

The organisation must continue to know:

**what the device is, who is responsible for it, whether it is safe to use, and what should happen to it next.**

The sections that follow break this lifecycle into the individual decisions required to keep a Wire Finance device accountable from acquisition through final disposal.