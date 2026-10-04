---
layout: lesson
title: "Maintenance and Repair"
series: wire-finance
parent: device-lifecycle
order: 6
description: "Updates, troubleshooting, repairs, and remediation are normal parts of endpoint life. The challenge is returning every device to its approved managed state before normal use resumes."
---

Devices do not remain frozen in the state they had on deployment day.

Normal operational maintenance may include:

- Windows updates
- Application updates
- Security-policy changes
- Configuration remediation
- Troubleshooting
- Hardware repair
- Battery replacement
- Defender investigations

The key principle is:

**maintenance should preserve or restore an approved state rather than create unmanaged configuration drift.**

#### Fixing the Problem Is Only Half the Job

Imagine IT changes a local setting while troubleshooting.

The poor outcome would be:

```text
Problem Fixed
      ↓
Temporary Configuration
Left Behind Forever
```

The desired outcome is:

```text
Problem Fixed
      ↓
Approved Intune Configuration Restored
      ↓
Device Re-Evaluated
```

The management model should remain authoritative after support activity.

#### Repair State

If:

`WF-LT-001`

develops a hardware problem, its lifecycle can move to:

```text
Lifecycle Status:
Repair
```

The flow becomes:

```text
In Service
      ↓
Repair
      ↓
Security / Data Considerations Reviewed
      ↓
Repair Completed
      ↓
Device Validated
      ↓
Return to In Service
```

The status gives Wire Finance a clear business record that the endpoint is temporarily unavailable.

#### Security and Data Considerations

Repair may create additional questions.

For example:

```text
Will an external vendor handle the device?

Does the device contain corporate data?

Does storage need to be removed or protected?

Has hardware replacement affected security features?
```

The answer depends on the repair scenario.

The lifecycle model makes sure those questions are considered before the machine simply returns to the user.

#### Return-to-Service Validation

Repair completion does not automatically mean:

```text
In Service
```

Before the status changes back, Wire Finance verifies that the endpoint is:

```text
Operational
      +
Managed
      +
Secure
      +
Compliant
```

Where relevant, the organisation should also confirm that the device still matches its intended security purpose.

A repaired PAW, for example, still needs to satisfy the PAW trust model.

#### Maintenance Decision

The complete maintenance principle is:

```text
Change Required
      ↓
Approved Maintenance / Repair
      ↓
Work Completed
      ↓
Managed State Restored
      ↓
Security Revalidated
      ↓
Compliance Revalidated
      ↓
Return to Service
```

The governing principle is:

**support activity ends when the approved device state has been restored, not merely when the immediate fault disappears.**

Sometimes, though, nothing is actually wrong with the laptop.

The user or business purpose simply changes.

**What happens when the same physical device needs to serve someone — or something — different?**