---
layout: lesson
title: "Device Provisioning"
series: wire-finance
parent: device-lifecycle
order: 3
description: "Follow a registered Wire Finance device as Autopilot, Microsoft Entra ID, and Microsoft Intune transform it into an organisationally managed Windows endpoint."
---

Registration tells Wire Finance which provisioning path the device should follow.

Provisioning performs the build.

The objective is to move from:

```text
Registered Hardware
```

to:

```text
Organisationally Managed Endpoint
```

#### The Provisioning Path

For a standard corporate device:

```text
Autopilot Registered
      ↓
CORPORATE Classification
      ↓
Standard Deployment Profile
      ↓
Windows OOBE
      ↓
User Authentication
      ↓
Microsoft Entra Join
      ↓
Automatic Intune Enrollment
      ↓
Applications + Configuration
      ↓
Security Controls
```

A PAW follows the same core cloud foundation but receives the privileged provisioning path and hardened configuration defined earlier.

#### Establishing Device Identity

Microsoft Entra Join establishes the organisational device identity.

```text
WF-LT-001
      ↓
Microsoft Entra Joined
      ↓
Wire Finance Device Identity
```

This tells the organisation:

**this Windows endpoint belongs to the Wire Finance tenant identity model.**

But Microsoft Entra Join is only one part of provisioning.

#### Establishing Management

Automatic Intune enrollment establishes management authority.

```text
Microsoft Entra Joined
        +
Microsoft Intune Enrolled
        ↓
Managed Endpoint Foundation
```

Microsoft Entra answers:

```text
What Device Is This?
```

Intune answers:

```text
How Do We Manage It?
```

Those responsibilities work together without being the same thing.

#### Configuration and Applications

The device can now begin receiving its approved configuration.

That may eventually include:

```text
Required Applications

Windows Configuration

Endpoint Security Policies

Update Configuration

Account Protection

Defender Onboarding
```

The exact implementation comes later.

At lifecycle-design level, the important point is that the device is moving toward an approved organisational state.

#### Provisioning Does Not Equal Readiness

A device may successfully:

```text
Join Entra
      +
Enroll in Intune
```

and still not be ready for use.

Perhaps:

```text
Required Apps Are Missing

Security Configuration Has Not Arrived

Encryption Is Incomplete

Compliance Has Not Been Evaluated
```

So:

```text
Provisioned
      ≠
Ready
```

That distinction leads directly into the next lifecycle stage.

#### Provisioning Decision

At the end of this stage:

```text
Lifecycle Status:
Provisioning
```

may still remain.

The technical identity and management relationship exist, but the acceptance gate has not yet been completed.

The governing principle is:

**provisioning establishes the organisational device identity and management foundation; validation determines whether the finished endpoint is actually ready for service.**

So how do we decide that the build is good enough?

**What must Wire Finance validate before the lifecycle changes from Provisioning to In Service?**