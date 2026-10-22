---
layout: lesson
title: "Security Hold and Recovery"
series: wire-finance
parent: device-lifecycle
order: 8
description: "Lost, stolen, high-risk, and suspected compromised devices need a lifecycle state of their own. Security Hold stops normal use while Wire Finance decides whether trust can be restored."
---

Not every lifecycle transition is planned.

A device may become:

```text
Lost

Stolen

High Risk

Suspected Compromised

Under Security Investigation
```

In these situations, Wire Finance moves the endpoint into:

```text
Lifecycle Status:
Security Hold
```

Security Hold means:

**the asset still exists, but normal use is no longer approved while its security condition is investigated.**

#### Security Hold Flow

The general process is:

```text
Security Event Reported
        ↓
Asset and Device Identified
        ↓
User / Device Risk Assessed
        ↓
Use Restricted or Device Contained
Where Appropriate
        ↓
Security Investigation
        ↓
Can Trust Be Re-Established?
       /                     \
     Yes                      No
      ↓                        ↓
Remediate /                Retirement
Reprovision                   ↓
      ↓                    Decommission
Validate
      ↓
Return to In Service
```

The lifecycle can therefore branch in two directions.

#### Recoverable Device

If Wire Finance determines that the endpoint can safely return:

```text
Security Hold
      ↓
Investigation
      ↓
Remediation / Reprovisioning
      ↓
Security Validation
      ↓
Compliance Validation
      ↓
Approved for Service
      ↓
In Service
```

The goal is to **re-establish trust**, not merely remove an alert.

#### Lost or Stolen Devices

Deleting a device record does not solve the problem of a missing physical asset.

The response still needs to consider:

```text
Corporate Data

User Access

Device Access

Asset Ownership

Security Risk

Investigation

Recovery Possibility
```

For example:

```text
WF-LT-001

Lifecycle Status:
Security Hold

Reason:
Stolen
```

provides a meaningful business state while Security handles the technical response.

#### Security Hold Is Not a Test State

This distinction matters.

```text
WF-TST-001
      ↓
Approved Controlled Failure
```

is lab activity.

But:

```text
WF-LT-001
      ↓
Unexpected Security Failure
```

is an operational security event.

An unexpectedly noncompliant or risky production device does not become a member of:

`SG-DEVICES-NONCOMPLIANT-TEST`

simply because something failed.

It is investigated as a genuine condition.

#### Privileged Workstations

The recovery bar is higher for:

`WF-PAW-001`

A suspected PAW compromise follows the stronger path:

```text
Suspected PAW Compromise
        ↓
STOP Privileged Use
        ↓
Investigate
        ↓
Contain
        ↓
Known-Good Reprovisioning
        ↓
Apply PAW Hardening
        ↓
Validate Security
        ↓
Validate Compliance
        ↓
Validate Privileged Access Path
        ↓
Return to Service
```

Privileged trust is not restored merely because the machine appears functional again.

#### When Recovery Is Not Appropriate

If Wire Finance cannot confidently return the endpoint to an approved state:

```text
Security Hold
      ↓
Recovery Not Approved
      ↓
Retirement Decision
```

This creates a clean transition from security response into device offboarding.

The governing principle is:

**Security Hold protects the organisation from treating an uncertain device as trusted while the organisation determines whether that trust can be restored.**

Sometimes it cannot.

**What happens when Wire Finance decides that a device should permanently leave active service?**