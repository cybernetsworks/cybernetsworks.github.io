---
layout: lesson
title: "Device Naming Convention"
series: wire-finance
parent: device-identity-provisioning
order: 2
description: "A device name should tell us something before we even open its record. Define a naming model that makes Wire Finance devices easier to identify, manage, and investigate."
---

We now know what kinds of devices Wire Finance expects to operate.

There are standard employee laptops.

- Privileged workstations.
- Deployment and test devices.
- Infrastructure systems.
- And deliberately isolated legacy systems.

But once those devices begin appearing across Microsoft Entra ID, Intune, Defender, asset records, support tickets, and security investigations, we need a consistent way to recognise them.

That is where the device naming convention comes in.

Wire Finance uses a standardised naming model to support:

- Asset management
- Troubleshooting
- Consistent provisioning
- Faster device identification
- Clear functional classification

The naming model deliberately avoids embedding personal, departmental, or location information that may change during the life of the device.

#### What Should a Device Name Tell Us?

Wire Finance uses a standard naming convention so devices can be identified quickly by:

- Organisation
- Functional device type
- Unique sequence number

The general format is:

```text
WF-<DEVICE-TYPE>-<NUMBER>
```

Where:

```text
WF
= Wire Finance

DEVICE-TYPE
= Functional device classification

NUMBER
= Unique sequential identifier
```

For example:

```text
WF-LT-001
```

can be read as:

```text
WF
↓
Wire Finance

LT
↓
Laptop / Standard Corporate Endpoint

001
↓
Unique Sequence Number
```

This gives us useful context before we even open the device record.

#### Naming Standard

Wire Finance currently uses the following naming patterns:

| Device Type | Prefix | Example | Purpose |
|---|---|---|---|
| **Standard laptop** | `WF-LT-` | `WF-LT-001` | Employee productivity endpoint |
| **Privileged workstation** | `WF-PAW-` | `WF-PAW-001` | Privileged administration |
| **Test device** | `WF-TST-` | `WF-TST-001` | Deployment and security testing |
| **Domain controller** | `WF-DC-` | `WF-DC-001` | Active Directory services |
| **Member server** | `WF-SRV-` | `WF-SRV-001` | General server workloads |
| **Hybrid identity server** | `WF-SYNC-` | `WF-SYNC-001` | Identity synchronisation |
| **Management jump server** | `WF-JUMP-` | `WF-JUMP-001` | Controlled administrative access |
| **Legacy test system** | `WF-LEGACY-` | `WF-LEGACY-001` | Isolated legacy-risk testing |

The functional prefix gives us a quick clue about the intended purpose of the device.

For example:

```text
WF-PAW-003
```

immediately tells us the device is intended to serve as a Privileged Access Workstation.

Likewise:

```text
WF-TST-002
```

tells us the device belongs to the test-device classification.

#### Why Use Function Instead of a Person's Name?

Wire Finance does **not** place employee names inside device names.

For example, we avoid names such as:

```text
OLIVIA-LAPTOP
JORDAN-ADMIN-PC
TAYLOR-SOC-DEVICE
```

That is intentional.

A device may be reassigned.

An employee may leave.

A user may change department.

A laptop may move from one business function to another.

If the user's identity is embedded in the hostname, the name can quickly become misleading.

Instead:

```text
WF-LT-001
```

remains a standard corporate laptop regardless of which approved employee is currently assigned to it.

The relationship between the **device and the user** should therefore be maintained as device-management information rather than permanently encoded in the hostname.

The same principle applies to department and location.

Wire Finance does not currently embed values such as:

```text
FINANCE
HR
SALES
LONDON
REMOTE
```

inside the device name.

Those characteristics may change throughout the life of the asset.

The functional classification is more stable.

#### Naming Rules

Wire Finance follows several rules when naming devices:

- Device names use **uppercase characters**
- Device type is represented by a consistent functional prefix
- A three-digit sequential number provides uniqueness
- Employee or user names are not embedded in the device name
- Department names are not embedded in the device name
- Location information is not embedded in the device name
- Existing device names should not be reused while the associated asset record remains active
- Renaming should only occur through an authorised IT / Endpoint Management process
- The name describes the device's intended function
- The name does **not** independently establish ownership, management, compliance, or trust

The final rule is especially important.

#### A Name Is a Label — Not a Security Control

Consider:

```text
WF-PAW-001
```

The name suggests that the device is intended to be a Privileged Access Workstation.

But simply naming a computer:

```text
WF-PAW-001
```

does not automatically make it a secure PAW.

The actual classification still depends on the controls behind the device.

Conceptually:

```text
Device Name
      ↓
Indicates Intended Function
```

while:

```text
Approved Provisioning
      +
Approved Device Classification
      +
Management
      +
Security Controls
      +
Compliance
      ↓
Actual Device State
```

So:

```text
WF-PAW-001
```

does **not** automatically mean:

```text
Trusted PAW
```

The same is true for a normal laptop.

A device named:

```text
WF-LT-001
```

is not automatically corporate-owned, Intune-managed, secure, or compliant just because the hostname follows the naming convention.

This continues the principle established earlier:

**device presence does not equal device trust — and neither does the device name.**

#### Naming and Device Ownership Are Different

It helps to separate the concepts.

The naming convention answers:

**What is this device intended to be?**

Ownership answers:

**Who actually controls this asset?**

Management answers:

**Is the device under organisational control?**

Compliance answers:

**Does the device currently meet our security requirements?**

So we should avoid treating one field as though it answers every question.

```text
Name
↓
Function

Ownership
↓
Asset Control

Management
↓
Operational Control

Compliance
↓
Current Security State
```

Together, those signals create a much more useful picture of the device than the hostname alone.

#### Naming Survives Reassignment

The naming model also works well with the device lifecycle.

Imagine:

```text
WF-LT-004
```

is initially assigned to one employee.

Later, that employee leaves and the laptop is securely reset, reprovisioned, and assigned to someone else.

The user has changed.

But the device is still fundamentally:

```text
Standard Corporate Endpoint
```

Its functional classification has not necessarily changed.

This is another reason Wire Finance avoids putting employee names inside device names.

The identity of the assigned user and the identity of the device are related, but they are not the same thing.

#### Naming Authority

Device names should not be chosen arbitrarily by individual users.

The naming authority for Wire Finance devices is:

**IT / Endpoint Management**

This means the Endpoint team owns the process for applying and maintaining the naming standard.

That provides consistency across:

- Microsoft Entra ID
- Microsoft Intune
- Microsoft Defender
- Asset records
- Support tickets
- Security investigations
- Administrative documentation

Consistent naming becomes especially useful during troubleshooting.

Instead of seeing several machines with unrelated names, an administrator or analyst can immediately recognise broad device purpose.

For example:

```text
WF-LT-014
→ Standard employee endpoint

WF-PAW-002
→ Privileged workstation

WF-TST-003
→ Test device

WF-DC-001
→ Domain controller
```

That context can save time before deeper investigation even begins.

#### Relationship With Windows Autopilot

For standard corporate endpoints, the naming convention will eventually become part of the Windows Autopilot and Intune provisioning workflow.

Conceptually:

```text
New Corporate Device
        ↓
Autopilot Classification
        ↓
Deployment Profile
        ↓
Wire Finance Naming Standard Applied
        ↓
WF-LT-###
```

The exact automated naming template will be configured later when the Autopilot deployment profile is implemented.

At this stage, we are defining the standard that future automation will enforce.

It is also important to separate the hostname from the other signals used during provisioning.

```text
Device Name
      ↓
Identifies Intended Function

Autopilot Classification
      ↓
Identifies Provisioning Intent

Management + Security State
      ↓
Establish Operational Control

Compliance
      ↓
Contributes to Trust Decisions
```

A name such as:

```text
WF-LT-001
```

tells us what the device is intended to be.

It does not, by itself, prove that the device was provisioned correctly, enrolled into Intune, secured, or compliant.

That distinction becomes important as we move deeper into the provisioning model.

#### Device Naming Record

The Wire Finance device naming standard can therefore be summarised as:

```text
Device Naming Standard:
WF-<DEVICE-TYPE>-<NUMBER>

Organisation Prefix:
WF

Device Type:
Functional classification

Numbering:
Three-digit sequential identifier

Examples:
WF-LT-001
WF-PAW-001
WF-TST-001
WF-DC-001
WF-SRV-001
WF-SYNC-001
WF-JUMP-001
WF-LEGACY-001

Naming Authority:
IT / Endpoint Management

Special Control:
A device name indicates intended function but does not
independently establish ownership, management, trust,
security state, or compliance.
```

#### We Can Name the Device — But Microsoft Still Needs to Know What It Is

At this point, a device name can tell us its intended function.

```text
WF-LT-001
→ Standard Corporate Endpoint

WF-PAW-001
→ Privileged Access Workstation

WF-TST-001
→ Deployment / Test Endpoint
```

But a hostname is still only a label.

Before Microsoft Entra ID, Intune, Conditional Access, or security tooling can make meaningful decisions about a device, that machine needs an identity inside the organisation.

For the first Wire Finance rollout, the design is cloud-first.

Standard employee devices are intended to become:

```text
Corporate-Owned Windows Device
        ↓
Microsoft Entra Joined
        ↓
Managed by Microsoft Intune
```

Hybrid identity will come later when on-premises Active Directory is introduced.

So the next question is:

**How does a physical Wire Finance device become a recognised Microsoft Entra device identity, and which join model should it use?**