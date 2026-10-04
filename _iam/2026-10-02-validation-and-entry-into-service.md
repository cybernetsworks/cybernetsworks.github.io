---
layout: lesson
title: "Validation and Entry Into Service"
series: wire-finance
parent: device-lifecycle
order: 4
description: "Reaching the Windows desktop is not the finish line. Wire Finance validates identity, management, security, applications, compliance, and assignment before approving a device for operational use."
---

Provisioning has completed.

But Wire Finance does not define success as:

```text
The Desktop Appeared
```

The device needs to prove that it has reached the approved operational state.

#### The Ready-for-Use Gate

For a standard corporate endpoint:

```text
Microsoft Entra Joined
        +
Microsoft Intune Managed
        +
Required Applications
        +
Security Controls
        +
Compliance
        +
Correct Assignment
        ↓
READY FOR USE
```

Only then should the lifecycle become:

```text
Lifecycle Status:
In Service
```

#### What Are We Validating?

The acceptance check includes areas such as:

| Area | Question |
|---|---|
| **Device identity** | Is the endpoint correctly Microsoft Entra joined? |
| **Management** | Is Intune managing the device? |
| **Applications** | Are required business applications available? |
| **Security** | Have the expected security controls arrived? |
| **Compliance** | Does the endpoint meet the appropriate compliance requirements? |
| **Assignment** | Is the correct user or administrator associated with the device? |
| **Purpose** | Does the resulting build match the approved device class? |

The validation standard differs by device purpose.

A standard laptop and a PAW should not have identical acceptance criteria.

#### Standard Endpoint

For:

`WF-LT-001`

the expected outcome is:

```text
Corporate
      +
Entra Joined
      +
Intune Managed
      +
Standard Security Configuration
      +
Compliant
      +
Assigned to Approved User
```

The endpoint can then operate as a normal employee device.

#### Privileged Workstation

For:

`WF-PAW-001`

the bar is higher.

The endpoint also needs its:

```text
PAW Classification

Dedicated Hardening

Privileged Compliance State

Approved Administrator

Privileged Access Path
```

validated before administrative use.

#### Operational Classification

A standard managed Windows endpoint can eventually satisfy:

`SG-DEVICES-WINDOWS-CORPORATE`

This reflects its operational state rather than merely its original provisioning intent.

So the lifecycle has moved from:

```text
How Should We Build It?
```

to:

```text
What Is It Now?
```

#### Lifecycle Transition

The normal progression is:

```text
Received
      ↓
Provisioning
      ↓
Validation
      ↓
In Service
```

That transition should be meaningful.

`In Service` means Wire Finance has accepted the endpoint for its intended business purpose.

The governing principle is:

**successful provisioning creates the device; successful validation approves it for use.**

The endpoint has now entered the longest part of its lifecycle.

**What happens while the device is operating normally inside Wire Finance?**