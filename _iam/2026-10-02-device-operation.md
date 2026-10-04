---
layout: lesson
title: "Device Operation"
series: wire-finance
parent: device-lifecycle
order: 5
description: "Most of a device's lifecycle is spent in service. See how Wire Finance keeps the business asset record aligned with Intune management, Defender security signals, and the device's approved purpose."
---

Most devices spend most of their lifecycle here:

```text
Lifecycle Status:
In Service
```

For example:

```text
WF-LT-001
      ↓
Assigned to Olivia Carter
      ↓
In Service
```

The endpoint is now performing its approved business function.

But operational use still requires ongoing governance.

#### Two Views of the Same Device

During operation, the Asset Register maintains the **business view**.

```text
Who Has It?

Who Owns It?

What Is Its Purpose?

What Lifecycle State Is It In?
```

Microsoft Intune and Defender maintain much of the **technical view**.

```text
Configuration

Updates

Compliance

Security Health

Threat Detection

Device Risk
```

Both views matter.

#### Business State Does Not Prove Technical Health

The Asset Register might say:

```text
WF-LT-001
Status: In Service
Assigned: Olivia Carter
```

But that does not independently prove:

```text
Compliant

Secure

Actively Checking In

Low Risk
```

Those conditions come from the technical management and security platforms.

Likewise, Intune can tell us a device is compliant without telling us whether the Asset Register has the correct business owner.

#### Operational Trust Can Change

A device can enter service in a healthy state and later drift.

For example:

```text
Healthy Endpoint
      ↓
Configuration Changes
      ↓
Security Condition Changes
      ↓
Compliance Re-Evaluated
```

or:

```text
Healthy Endpoint
      ↓
Threat Detected
      ↓
Defender Risk Increases
      ↓
Trust Changes
```

Operational trust is therefore not permanent.

It must continue to be evaluated.

#### Device Purpose Still Matters

An `In Service` state can describe several kinds of endpoint.

```text
WF-LT-###
      ↓
Employee Productivity
```

```text
WF-PAW-###
      ↓
Privileged Administration
```

```text
WF-TST-###
      ↓
Approved Lab Activity
```

Their lifecycle state may be similar.

Their security purpose is not.

#### Special Operational States

A device can temporarily leave ordinary In Service operation for another controlled state:

```text
Pilot/Test

Repair

Reassignment

Security Hold
```

If the reason is temporary and the endpoint can be returned to a trusted state, it may later re-enter normal operation.

The governing principle is:

**In Service means approved for the device's current purpose, not permanently trusted regardless of future changes.**

And change is inevitable.

**How does Wire Finance maintain and repair endpoints without letting troubleshooting or hardware work create uncontrolled configuration drift?**