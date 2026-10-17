---
layout: lesson
title: "Device Identity and Join Model"
series: wire-finance
parent: device-identity-provisioning
order: 3
description: "A device can be company-owned without being joined, managed, compliant, or trusted. Define how Wire Finance devices receive an identity and how Microsoft Entra Join, Intune, and Autopilot fit together."
---

We now know what kinds of devices Wire Finance operates.

We know who owns them.

And we have a naming convention that tells us their intended function.

But there is still an important gap.

A laptop can be:

```text
Corporate-Owned
```

without yet being:

```text
Microsoft Entra Joined
```

And a device can exist inside Microsoft Entra ID without automatically being:

```text
Intune Managed
Compliant
Secure
Trusted
```

Those are different states.

This is where the **Device Identity and Join Model** begins.

### Four Different Questions

It is easy to treat ownership, identity, management, and compliance as though they mean the same thing.

They do not.

Each answers a different question.

```text
Ownership
   ↓
Who controls the physical asset?

Device Identity
   ↓
How does Microsoft recognise the device?

Management
   ↓
Which platform configures and manages it?

Compliance
   ↓
Does it currently meet our requirements?
```

Wire Finance deliberately keeps these concepts separate.

That means:

```text
Company-Owned
      ≠
Microsoft Entra Joined

Microsoft Entra Joined
      ≠
Intune Managed

Intune Managed
      ≠
Compliant

Compliant
      ≠
Automatically Trusted
```

Each stage gives us another piece of information about the endpoint.

### Why Cloud-First Fits Wire Finance

Wire Finance already operates around:

```text
Microsoft 365
      +
Microsoft Entra ID
      +
Microsoft Intune
```

What it does not yet have is a production on-premises Active Directory environment.

There is therefore no reason to introduce an on-premises dependency simply so new employee laptops can receive an organisational identity.

The initial model can remain cloud-first:

```text
Employee
   ↓
Microsoft Entra User Identity

Device
   ↓
Microsoft Entra Device Identity

Management
   ↓
Microsoft Intune
```

On-premises Active Directory and hybrid identity will be introduced later because the project has a reason to study them — not because every new Wire Finance endpoint requires them from day one.

### The Initial Wire Finance Model

New corporate Windows endpoints follow this architecture:

```text
Corporate-Owned Windows Device
        ↓
Windows Autopilot
        ↓
Microsoft Entra Join
        ↓
Microsoft Intune Enrollment
        ↓
Configuration + Security Controls
        ↓
Compliance Evaluation
```

![Wire Finance Device Identity and Join Model]({{ '/assets/images/active-directory/device_identity_join_model.png' | relative_url }})

Each technology has a different responsibility.

**Windows Autopilot** helps establish the provisioning experience.

**Microsoft Entra Join** establishes the organisational device identity.

**Microsoft Intune** provides management and configuration.

Later security and compliance controls determine whether the endpoint continues to meet Wire Finance requirements.

The technologies work together, but they are not interchangeable.

### Microsoft Entra Device Identity States

Three Microsoft Entra device states matter to the wider Wire Finance design.

| Device Identity | Meaning | Typical Scenario | Wire Finance Position |
|---|---|---|---|
| **Microsoft Entra joined** | Device is directly joined to the Wire Finance Microsoft Entra tenant | Corporate Windows endpoint | Primary model |
| **Microsoft Entra registered** | A work or school identity is associated with a device without full organisational join | Personal / BYOD device | Outside initial deployment scope |
| **Microsoft Entra hybrid joined** | Device is joined to on-premises Active Directory and also represented in Microsoft Entra | Existing domain-joined environment | Future hybrid lab |

These states represent different relationships between a device and the organisation.

For the initial Wire Finance rollout, the rule is straightforward:

```text
NEW CORPORATE WINDOWS ENDPOINT
        ↓
Microsoft Entra Joined
```

not:

```text
Microsoft Entra Registered
```

and not:

```text
Microsoft Entra Hybrid Joined
```

### Microsoft Entra Joined

Microsoft Entra Join is the standard identity model for new Wire Finance corporate Windows endpoints.

The device joins the Wire Finance Microsoft Entra tenant directly rather than first depending on an on-premises Active Directory domain.

The initial client-device classes use this model:

```text
WF-LT-###
WF-PAW-###
WF-TST-###
        ↓
Microsoft Entra Joined
```

The join type is the same even though the devices serve different purposes.

A standard corporate laptop, a privileged workstation, and a test endpoint can all be Microsoft Entra joined.

What changes is how each device is classified, provisioned, targeted, secured, and evaluated.

This gives us an important principle:

**same join model does not mean same security posture.**

### Microsoft Entra Registered

Microsoft Entra registered represents a different relationship.

Conceptually:

```text
Personal Device
      ↓
User Adds Wire Finance Work Account
      ↓
Microsoft Entra Registered
```

That does not make the device equivalent to a corporate Wire Finance endpoint.

```text
Microsoft Entra Registered
        ≠
Corporate-Managed Endpoint
```

This model is most relevant to a possible future BYOD scenario.

Because personally owned Windows endpoints are outside the initial managed fleet, Microsoft Entra registered is **not** the standard Wire Finance endpoint model.

### Microsoft Entra Hybrid Joined

Microsoft Entra hybrid joined devices belong to both an on-premises Active Directory environment and Microsoft Entra.

Conceptually:

```text
On-Premises Active Directory
        +
Microsoft Entra ID
        ↓
Microsoft Entra Hybrid Joined
```

Wire Finance does not need this model during the initial cloud-first rollout.

Later, when on-premises Active Directory is introduced, a separate hybrid lab can be used to explore traditional domain-joined devices and compare them with the existing cloud-native fleet.

The objective is not to convert every existing Wire Finance endpoint to hybrid join.

Instead, the later environment will allow us to understand why cloud-native and hybrid architectures behave differently.

### Join Decision Matrix

The current position can be summarised as:

| Device | Identity Model | Intune | Autopilot | Position |
|---|---|---|---|---|
| **`WF-LT-###`** | Microsoft Entra joined | Yes | Yes | Standard corporate endpoint |
| **`WF-PAW-###`** | Microsoft Entra joined | Yes | Yes | Privileged endpoint |
| **`WF-TST-###`** | Microsoft Entra joined | Yes | Where applicable | Test / validation |
| **Personal / BYOD** | Microsoft Entra registered where applicable | Future decision | No standard deployment | Out of scope initially |
| **Existing AD workstation** | Microsoft Entra hybrid joined | Future decision | Separate scenario | Future hybrid lab |
| **Servers / infrastructure** | Separate server identity model | Separate management path | No standard client Autopilot | Future |
| **Legacy systems** | Isolated according to test purpose | Not part of normal trusted fleet | No | Lab only |

This makes another important point clear:

**client-device identity and server identity are different design problems.**

A domain controller is not simply another employee endpoint.

A legacy test system is not a trusted corporate laptop.

And a personal device does not become corporate-owned simply because somebody signs into Microsoft 365 from it.

### Autopilot, Entra Join, and Intune

These three concepts are closely connected, but they perform different jobs.

A normal Wire Finance employee provisioning journey looks broadly like:

```text
Approved Corporate Device
        ↓
Registered for Windows Autopilot
        ↓
Provisioning Experience Starts
        ↓
User Authenticates
        ↓
Device Joins Microsoft Entra
        ↓
Device Enrolls into Microsoft Intune
```

The detailed Autopilot classification, profiles, group targeting, and provisioning flow are covered later in the Device Provisioning Model.

For now, the important distinction is:

```text
Autopilot
   ↓
Provisioning

Microsoft Entra
   ↓
Device Identity

Microsoft Intune
   ↓
Device Management
```

### User Identity and Device Identity Are Separate

Consider Olivia Carter receiving a corporate laptop.

```text
Olivia Carter
      ↓
User Identity

Corporate Laptop
      ↓
Device Identity
```

During a user-driven provisioning process, Olivia authenticates as herself.

The endpoint then establishes its own identity inside Microsoft Entra.

The person and the device become related.

They do not become the same identity.

This distinction becomes important later when Wire Finance begins making decisions that consider both:

```text
Who is the user?
        +
What device are they using?
```

### Join Is Not the Same as Intune Enrollment

This distinction is critical.

**Microsoft Entra Join** answers:

> Which organisation does this Windows device belong to for identity purposes?

**Microsoft Intune enrollment** answers:

> Which management service is responsible for configuring and managing the endpoint?

So:

```text
Device Identity
      ≠
Device Management
```

Wire Finance deliberately wants both:

```text
Microsoft Entra Joined
        +
Intune Enrolled
        ↓
Corporate Managed Endpoint
```

But the existence of one state does not prove the other.

A device may have a Microsoft Entra identity without being correctly managed.

That is why both states need to be validated separately.

### Initial Automatic MDM Enrollment

Wire Finance does not immediately apply automatic Intune enrollment to the entire workforce.

The first deployment uses a controlled pilot.

```text
Automatic MDM Enrollment
        ↓
User Scope = Some
        ↓
SG-PILOT-IDENTITY
        ↓
Validate Enrollment Journey
        ↓
Expand Later
```

This gives the initial pilot users access to the automatic enrollment journey without immediately exposing the full 25-person workforce to an unvalidated configuration.

The detailed pilot strategy is covered later in the Device Provisioning Model.

### Joining a Device Does Not Grant Local Administrator Rights

Receiving a Wire Finance laptop does not mean an employee should become a permanent Windows administrator.

The normal access design already established:

```text
Local Administrator Rights
        =
None by Default
```

The expected employee journey is therefore:

```text
Employee
      ↓
Corporate Endpoint
      ↓
Microsoft Entra Join
      ↓
Intune Enrollment
      ↓
Standard Windows User
```

Administrative support and recovery should use controlled administrative mechanisms rather than permanently elevating normal employees.

Join status and local privilege are separate decisions.

### PAWs Use the Same Join Foundation

A Privileged Access Workstation uses the same core cloud identity foundation:

```text
Microsoft Entra Joined
        +
Intune Managed
```

as a standard corporate endpoint.

That does not make them equivalent.

```text
Standard Endpoint
        ↓
Normal Productivity

Privileged Access Workstation
        ↓
Controlled Administration
```

The difference comes from classification, targeting, security configuration, compliance expectations, and the activities permitted on the device.

There is no separate "privileged join" type.

The same identity platform can support different device-security tiers.

The dedicated PAW page will define that model in more detail.

### A Note About Device Names

The join model does not depend on the Windows hostname.

A device following the Wire Finance naming convention may ultimately be represented as:

```text
WF-LT-001
```

but the name itself does not determine whether the endpoint is Microsoft Entra joined or Intune managed.

Likewise, a newly provisioned device may temporarily use a Windows-generated hostname while still establishing the correct device identity and management state.

This keeps the earlier naming principle intact:

**the device name describes intended function; it does not create trust.**

### Device Trust Builds Progressively

We can now connect the pieces of the device model.

```text
Device Acquired
      ↓
Corporate Ownership Established
      ↓
Provisioning Intent Established
      ↓
Microsoft Entra Joined
      ↓
Intune Enrolled
      ↓
Security Controls Applied
      ↓
Compliance Evaluated
      ↓
Device State Can Influence Access Decisions
```

No individual stage means:

```text
This Device Is Trusted
```

on its own.

For example:

```text
Corporate-Owned
```

does not automatically mean:

```text
Compliant
```

And:

```text
Microsoft Entra Joined
```

does not automatically mean:

```text
Secure
```

Each stage contributes another signal about the endpoint.

### Wire Finance Design Decision

The Device Identity and Join Model can therefore be summarised as:

```text
Primary Client Join Type:
Microsoft Entra Joined

Primary Device Management:
Microsoft Intune

Primary Provisioning Platform:
Windows Autopilot

Initial Autopilot Experience:
User-Driven

Automatic MDM Enrollment:
Required for the managed corporate journey

Initial MDM Scope:
SG-PILOT-IDENTITY

Normal Employee Rights:
Standard User

Microsoft Entra Registered:
Possible future BYOD scenario

Microsoft Entra Hybrid Joined:
Future Active Directory / hybrid lab

Servers:
Separate identity and provisioning model

Core Principle:
Ownership, joining, management, compliance,
and trust are separate device states.
```

That gives Wire Finance a consistent device-identity foundation.

But once more devices begin appearing in Microsoft Entra ID and Intune, the organisation needs a way to distinguish different populations.

Corporate endpoints need one kind of targeting.

Privileged devices may need another.

Autopilot devices need provisioning classification.

Pilot devices need controlled deployment.

Test devices may need deliberate non-compliant scenarios.

The next question is therefore:

**How should Wire Finance organise device identities into groups so that provisioning, management, testing, and security policies reach the correct endpoints?**