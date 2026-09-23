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

And we have a consistent naming convention that tells us their intended function.

But there is still an important gap.

A laptop can be:

```text
Corporate Owned
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

#### Four Different Questions

It is easy to treat device ownership, identity, management, and compliance as though they mean the same thing.

They do not.

Each one answers a different question.

```text
Ownership
↓
Who controls the physical asset?

Device Identity
↓
How does Microsoft recognise the device?

Management
↓
Who configures and controls the endpoint?

Compliance
↓
Does the device currently meet our requirements?
```

Wire Finance deliberately keeps these concepts separate.

That means:

```text
Company Purchased
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

Each stage contributes another piece of information about the device.

#### Why Cloud-First Fits Wire Finance

Wire Finance already has:

```text
Microsoft 365
      +
Microsoft Entra ID
      +
Microsoft Intune
```

What it does not yet have is a production on-premises Active Directory environment.

That means there is no current requirement to introduce an on-premises dependency simply so new laptops can receive an identity.

The first endpoint architecture can therefore remain straightforward:

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

Hybrid identity will be introduced later because the project has a reason to study it — not because every new device needs it from day one.

#### The Initial Wire Finance Model

The first Wire Finance endpoint deployment is **cloud-first**.

New corporate Windows endpoints will use:

```text
Corporate-Owned Windows Device
        ↓
Windows Autopilot
        ↓
Microsoft Entra Join
        ↓
Microsoft Intune Enrollment
        ↓
Security Controls
        ↓
Compliance Evaluation
```

Microsoft Entra Join provides the device identity.

Windows Autopilot helps automate provisioning.

Microsoft Intune becomes the management platform.

This gives Wire Finance a cloud-native device foundation without requiring on-premises Active Directory during the first phase.

![Wire Finance Device Identity and Join Model]({{ '/assets/images/device_identity_join_model.png' | relative_url }})

#### Microsoft Entra Device States

There are three device identity states that matter to the wider Wire Finance design.

They may all create device-related records in Microsoft services, but they represent different deployment models.

| Device Identity | Meaning | Typical Use | Wire Finance Position |
|---|---|---|---|
| **Microsoft Entra joined** | Device is directly joined to the Wire Finance Microsoft Entra tenant | Corporate Windows devices | Primary model |
| **Microsoft Entra registered** | A work or school identity is associated with a device without full organisational join | Personal / BYOD devices | Outside initial deployment scope |
| **Microsoft Entra hybrid joined** | Device is joined to on-premises Active Directory and also represented in Microsoft Entra | Existing domain-joined estates | Future legacy / lab scenario |

The important part is not simply remembering the three names.

It is understanding that they represent different relationships between the device and the organisation.

For the initial rollout, the rule is simple:

```text
NEW CORPORATE WINDOWS DEVICE
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

#### Microsoft Entra Joined

Microsoft Entra Join is the standard model for new Wire Finance corporate Windows endpoints.

The device becomes directly associated with the Wire Finance Entra tenant rather than depending on an on-premises Active Directory domain first.

For the initial deployment, this applies to:

```text
WF-LT-###
WF-PAW-###
WF-TST-###
```

So the standard relationship becomes:

```text
Wire Finance Corporate Device
        ↓
Microsoft Entra Joined
        ↓
Cloud Device Identity
```

For example:

```text
WF-LT-001
      ↓
Microsoft Entra ID
      ↓
Join Type:
Microsoft Entra Joined
```

This keeps the first phase cloud-native and avoids introducing an on-premises dependency before the project actually needs one.

#### Microsoft Entra Registered

Microsoft Entra registered devices represent a different relationship.

This model is more appropriate for scenarios where a work or school identity is associated with a device without fully joining that device to the organisation.

Conceptually:

```text
Personal Laptop
      ↓
User Adds Wire Finance Work Account
      ↓
Microsoft Entra Registered
```

That is **not equivalent to corporate join**.

```text
Microsoft Entra Registered
        ≠
Corporate-Managed Wire Finance Endpoint
```

For Wire Finance, this maps most naturally to a possible future BYOD model.

But BYOD is outside the initial managed-device deployment.

So:

```text
Microsoft Entra Registered
        ↓
Possible Future BYOD
        ↓
Not the Current Corporate Standard
```

#### Microsoft Entra Hybrid Joined

Microsoft Entra hybrid joined devices belong to both worlds:

```text
On-Premises Active Directory
        +
Microsoft Entra ID
```

That model becomes relevant when an organisation already has traditional domain-joined Windows devices and wants those identities represented in Microsoft Entra.

Wire Finance does not need that model yet.

On-premises Active Directory is planned for a later phase of the project.

When that happens, Hybrid Join will be introduced deliberately so we can compare:

```text
Cloud-Native Device
        ↓
Microsoft Entra Joined
```

with:

```text
Traditional Domain Device
        ↓
Active Directory Joined
        +
Microsoft Entra Hybrid Joined
```

The future hybrid lab is therefore intended for learning and legacy compatibility rather than becoming the default model for newly provisioned Wire Finance endpoints.

#### Primary Join Model

For the first endpoint deployment, the decision is straightforward.

```text
Standard Corporate Endpoint
WF-LT-###
        ↓
Microsoft Entra Joined
```

```text
Privileged Access Workstation
WF-PAW-###
        ↓
Microsoft Entra Joined
```

```text
Deployment / Test Endpoint
WF-TST-###
        ↓
Microsoft Entra Joined
```

All three use the same core cloud identity model.

What changes later is **how they are provisioned, grouped, secured, and evaluated**.

That distinction becomes particularly important for privileged workstations.

#### Join Decision Matrix

The Wire Finance position can be summarised like this:

| Device | Identity Model | Intune | Autopilot | Status |
|---|---|---|---|---|
| **`WF-LT-###`** | Microsoft Entra joined | Yes | Yes | Standard |
| **`WF-PAW-###`** | Microsoft Entra joined | Yes | Yes | PAW standard |
| **`WF-TST-###`** | Microsoft Entra joined | Yes | Yes where applicable | Lab / test |
| **Personal / BYOD** | Microsoft Entra registered | Possible later | No standard deployment | Out of scope initially |
| **Existing AD workstation** | Microsoft Entra hybrid joined | Possible | Future / legacy | Future |
| **`WF-DC-*` / `WF-SRV-*`** | Separate server identity model | Separate | No | Future |
| **`WF-LEGACY-*`** | Isolated | No trusted corporate enrollment | No | Lab only |

This makes one important point clear:

**client device identity and server identity are not the same problem.**

A domain controller is not simply a large employee laptop.

A legacy test system is not a trusted corporate endpoint.

And a personal device does not become a corporate device simply because a user signs into Microsoft 365 from it.

#### Autopilot and Microsoft Entra Join

The normal employee deployment will use a **user-driven Windows Autopilot journey**.

Conceptually:

```text
Automatic Intune Enrollment Configured
        ↓
Approved Users Allowed to Join Devices
        ↓
Device Registered with Autopilot
        ↓
Device Group Assigned
        ↓
Autopilot Profile Assigned
        ↓
User Starts Device
        ↓
User Authenticates
        ↓
Device Joins Microsoft Entra
        ↓
Device Enrolls into Intune
```

We will configure those pieces later during the actual Autopilot implementation.

For now, the important thing is understanding the relationship between them.

Consider Olivia Carter receiving a corporate laptop:

```text
WF-LT-001
      ↓
Windows Autopilot
      ↓
Olivia Signs In
      ↓
Microsoft Entra Join
      ↓
Intune Enrollment
```

Olivia's user identity authenticates during the provisioning process.

But the endpoint receives its own **device identity**.

```text
Olivia Carter
      ↓
User Identity

WF-LT-001
      ↓
Device Identity
```

The person and the endpoint become related.

They do not become the same identity.

#### Join Is Not the Same as Intune Enrollment

This distinction is critical.

**Microsoft Entra Join** answers:

> Which organisation does this device belong to for identity and authentication?

**Microsoft Intune enrollment** answers:

> Which management platform controls and configures this device?

So:

```text
Device Identity
      ≠
Device Management
```

For Wire Finance, we deliberately combine both:

```text
Microsoft Entra Joined
        +
Intune Enrolled
        ↓
Corporate Managed Endpoint
```

But one does not automatically prove the other.

A device could have a Microsoft Entra identity without being correctly managed.

That is why both states need to be understood separately.

#### Initial MDM Enrollment Scope

Wire Finance will not enable automatic Intune enrollment for the entire organisation on day one.

The pilot phase is deliberately smaller.

The planned initial model is:

```text
MDM User Scope
      =
Some
```

targeting:

```text
SG-PILOT-IDENTITY
```

So:

```text
Pilot Users
      ↓
SG-PILOT-IDENTITY
      ↓
Automatic MDM Enrollment
      ↓
Validate
      ↓
Expand Later
```

This lets the initial pilot identities test the enrollment journey without immediately affecting the full 25-person workforce.

Once the process has been validated, the enrollment scope can be expanded to the wider approved corporate population.

That gives us another example of the Wire Finance rollout principle:

**test with a controlled population before expanding broadly.**

#### Joining a Device Does Not Make the User an Administrator

Another boundary needs to be explicit.

Receiving or joining a Wire Finance device does **not** grant the employee permanent local administrator rights.

The existing user-access design already established:

```text
Local Administrator Rights:
None by Default
```

So the expected journey is:

```text
Olivia Carter
      ↓
Receives WF-LT-001
      ↓
Microsoft Entra Join
      ↓
Intune Enrollment
      ↓
Standard Windows User
```

not:

```text
Olivia Carter
      ↓
Local Administrators Group
```

Administrative recovery and endpoint support will later use controlled privileged mechanisms rather than permanently elevating ordinary users.

The planned model includes areas such as:

- Dedicated administrative identities
- Intune RBAC
- Windows LAPS
- Controlled endpoint-support privileges

This keeps normal productivity and administrative privilege separate.

#### PAWs Use the Same Join Model — But Not the Same Trust Level

Privileged Access Workstations use the same core device identity foundation as standard corporate laptops:

```text
Microsoft Entra Joined
        +
Intune Managed
```

But that does not make the two device types equivalent.

```text
WF-LT-001
      ↓
Standard Corporate Endpoint
```

while:

```text
WF-PAW-001
      ↓
Privileged Administrative Endpoint
```

The difference comes from the controls around the device:

```text
Autopilot Classification
        +
Device Group Membership
        +
Security Configuration
        +
Compliance Policy
        +
Conditional Access
```

not from choosing a different Entra join type.

There is no special:

```text
Super Privileged Join
```

Instead, the same identity platform supports different device trust tiers.

That gives us a useful principle:

**same join model does not mean same security posture.**

#### The Future Hybrid Phase

Later in the project, Wire Finance will introduce on-premises Active Directory.

That gives us an opportunity to build a deliberate comparison environment rather than converting the existing cloud-native fleet automatically.

For example:

```text
Cloud-Native Endpoint
WF-LT-001
      ↓
Microsoft Entra Joined
```

compared with:

```text
Legacy / Hybrid Test Endpoint
WF-TST-HYB-001
      ↓
Active Directory Joined
      ↓
Microsoft Entra Hybrid Joined
```

That future lab can help us compare areas such as:

- Authentication
- Group Policy
- Intune management
- Conditional Access
- Single sign-on
- Troubleshooting
- Logging
- Operational overhead

The goal is not simply to prove that Hybrid Join exists.

It is to understand **why cloud-native and hybrid architectures behave differently**.

Existing cloud-native Wire Finance devices will therefore not automatically be converted into Hybrid Join devices when Active Directory arrives.

#### Device Trust Builds Progressively

We can now connect the pieces from the previous lessons.

Trust does not appear at the moment the device is purchased.

It does not appear when we assign a hostname.

And it does not appear simply because a device object exists in Microsoft Entra.

The trust journey develops progressively:

```text
Device Purchased
      ↓
Corporate Ownership Established
      ↓
Autopilot Registered
      ↓
Microsoft Entra Joined
      ↓
Intune Enrolled
      ↓
Security Controls Applied
      ↓
Compliance Evaluated
      ↓
Conditional Access Can Use Device State
```

Each stage adds context.

No single stage by itself means:

```text
This Device Is Trusted
```

For example:

```text
Corporate Owned
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

Instead, each stage gives Wire Finance another signal about the endpoint.

That continues the principle established earlier:

**device presence does not equal device trust.**

#### Wire Finance Design Decision

The current device identity model can therefore be summarised as:

```text
Primary Client Join Type:
Microsoft Entra Joined

Primary Device Management:
Microsoft Intune

Primary Provisioning:
Windows Autopilot

Deployment Mode:
User-Driven

Automatic MDM Enrollment:
Required

Initial Enrollment Scope:
SG-PILOT-IDENTITY

Standard Users:
No Local Administrator Rights by Default

Microsoft Entra Registered:
Future BYOD Only

Microsoft Entra Hybrid Joined:
Future Legacy / On-Premises Lab Scenario

Servers:
Separate Identity / Provisioning Model

Design Principle:
Joining, managing, and trusting a device are separate
security decisions.
```

That gives us a consistent device identity foundation.

But knowing how a device joins the organisation is still only part of the problem.

Once dozens of endpoints begin appearing in Microsoft Entra ID and Intune, we need a way to separate:

```text
Corporate Devices

Privileged Devices

Pilot Devices

Autopilot Devices

Non-Compliant Test Devices
```

And ideally, some of that classification should happen automatically.

So the next question is:

**How should Wire Finance organise its device identities into groups so provisioning, testing, management, and security policies reach the right endpoints?**