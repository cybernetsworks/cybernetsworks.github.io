---
layout: lesson
title: "Reassignment and Internal Reuse"
series: wire-finance
parent: device-lifecycle
order: 7
description: "A useful device should not be discarded simply because its user changes. Wire Finance uses controlled reset, reprovisioning, reassignment, and validation to prepare corporate hardware for its next approved purpose."
---

A user's relationship with a device may end long before the hardware reaches the end of its useful life.

Suppose Olivia receives a replacement laptop.

`WF-LT-001`

is still healthy.

Wire Finance does not need to dispose of it.

The device can be reassigned.

#### Reassignment Is Not Retirement

The distinction is fundamental:

```text
REASSIGNMENT

Device remains a Wire Finance asset
        ↓
New user or approved purpose
```

versus:

```text
RETIREMENT

Device leaves active organisational service
```

For example:

```text
Olivia → Maya
```

could be reassignment.

But:

```text
Wire Finance → Sold / Transferred / Disposed
```

belongs to retirement and decommissioning.

#### Reassignment Flow

The expected process is:

```text
WF-LT-001
Assigned to Olivia
        ↓
Reassignment Approved
        ↓
Outgoing User Data and Associations Removed
        ↓
Device Returned to Approved State
        ↓
Security and Compliance Validated
        ↓
New User Assigned
        ↓
Asset Register Updated
        ↓
Return to In Service
```

The lifecycle status can temporarily become:

```text
Reassignment
```

#### Internal Reuse Requires a Known-Good State

Internal reuse should not look like:

```text
Collect Laptop
      ↓
Hand It to Someone Else
```

The next user should receive a clean, controlled corporate state rather than inheriting the previous employee's data, settings, or unintended configuration.

For supported Entra-joined and Intune-managed Autopilot devices, a controlled reset or reprovisioning process may be appropriate.

Conceptually:

```text
Existing Corporate Device
        ↓
Controlled Reset / Reprovision
        ↓
Previous User State Removed
        ↓
Corporate Management Relationship Retained
Where Appropriate
        ↓
Policies Reapplied
        ↓
New User
```

The exact action will depend on the implementation and lifecycle scenario.

The design requirement is the important part:

**return the device to an approved state before reassignment.**

#### Update the Business Record

Technical reprovisioning is only half of reassignment.

The Asset Register must also change.

For example:

```text
Previous Assignment:
Olivia Carter

New Assignment:
Maya Patel

Lifecycle Status:
In Service
```

The business and technical records need to agree.

#### A Change of Purpose Is More Significant

Sometimes the new purpose changes the device's security tier.

For example:

```text
Standard Corporate Endpoint
        ↓
Privileged Access Workstation
```

or:

```text
Privileged Access Workstation
        ↓
Standard Corporate Endpoint
```

That cannot be handled as a simple user reassignment.

The process becomes:

```text
New Security Purpose Approved
        ↓
Correct Classification
        ↓
Controlled Reprovisioning
        ↓
Correct Security Baseline
        ↓
Correct Compliance Requirements
        ↓
Validation
        ↓
Return to Service
```

Changing only:

```text
Device Name
```

or:

```text
Autopilot Group Tag
```

does not establish the new trusted state.

#### Reassignment Decision

The governing principle is:

**if the physical asset remains inside Wire Finance, reuse should preserve organisational ownership while removing the previous user's state and re-establishing the correct approved configuration.**

Not every lifecycle change is planned, however.

Sometimes the device is lost, stolen, risky, or potentially compromised.

**What happens when Wire Finance can no longer assume an In Service endpoint is safe to use?**