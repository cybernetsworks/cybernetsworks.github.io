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

Privileged workstations.

Deployment and test devices.

Infrastructure systems.

And deliberately isolated legacy systems.

As those devices begin appearing across asset records, Microsoft Entra ID, Intune, Defender, support tickets, logs, and security investigations, we need a consistent way to recognise them.

That is where the device naming convention comes in.

Wire Finance uses a standard naming model to support:

- asset management
- troubleshooting
- consistent administration
- faster device identification
- clear functional classification
- security investigations

The naming model deliberately avoids embedding personal, departmental, or location information that may change during the life of the device.

### What Should a Device Name Tell Us?

Wire Finance wants a device name to communicate three things:

```text
Organisation
      +
Functional Device Type
      +
Unique Sequence
```

The design standard is:

```text
WF-<DEVICE-TYPE>-<NUMBER>
```

Where:

```text
WF
=
Wire Finance

DEVICE-TYPE
=
Functional device classification

NUMBER
=
Three-digit sequential asset identifier
```

For example:

```text
WF-LT-001
```

can be understood as:

```text
WF
↓
Wire Finance

LT
↓
Standard Laptop

001
↓
Sequential Identifier
```

The result gives us useful context before we even open the device record.

### Wire Finance Naming Standard

Wire Finance currently defines the following naming patterns:

| Device Type | Prefix | Example | Purpose |
|---|---|---|---|
| **Standard laptop** | `WF-LT-` | `WF-LT-001` | Everyday employee productivity |
| **Privileged Access Workstation** | `WF-PAW-` | `WF-PAW-001` | Controlled privileged administration |
| **Deployment / test device** | `WF-TST-` | `WF-TST-001` | Endpoint, deployment, compliance, and security testing |
| **Domain controller** | `WF-DC-` | `WF-DC-001` | Active Directory services |
| **Member server** | `WF-SRV-` | `WF-SRV-001` | General server workloads |
| **Hybrid identity server** | `WF-SYNC-` | `WF-SYNC-001` | Identity synchronisation |
| **Management jump server** | `WF-JUMP-` | `WF-JUMP-001` | Controlled administrative access |
| **Legacy test system** | `WF-LEGACY-` | `WF-LEGACY-001` | Isolated legacy-risk testing |

The functional prefix provides a quick indication of the device's intended purpose.

For example:

```text
WF-PAW-003
```

suggests a Privileged Access Workstation.

Likewise:

```text
WF-TST-002
```

identifies a deployment or test device.

The naming standard helps us recognise purpose.

It does not establish security state.

### Why Use Function Instead of a Person's Name?

Wire Finance does not place employee names inside device names.

We deliberately avoid names such as:

```text
OLIVIA-LAPTOP
JORDAN-ADMIN-PC
TAYLOR-SOC-DEVICE
```

A device may be reassigned.

An employee may leave.

A user may change department.

A laptop may move between approved users while remaining the same physical corporate asset.

If a person's identity is permanently embedded in the hostname, the name can quickly become misleading.

Instead:

```text
WF-LT-001
```

can remain a standard corporate laptop regardless of which authorised employee is currently assigned to it.

The relationship between the device and its current user belongs in device-management and asset information rather than in the hostname itself.

### Why Department and Location Are Also Excluded

Wire Finance does not currently embed department or location information such as:

```text
FINANCE
HR
SALES
LONDON
REMOTE
```

inside device names.

Those values may change during the asset's lifetime.

For example, a laptop could be reassigned from one employee to another or from one department to another without becoming a fundamentally different type of device.

The functional device classification is more stable.

### Naming Rules

Wire Finance follows several naming rules:

- device names use uppercase characters as the organisational convention
- each device class uses a consistent functional prefix
- the design uses a three-digit sequential asset number
- employee names are not embedded in the hostname
- department names are not embedded in the hostname
- location information is not embedded in the hostname
- naming identifiers should not be reused casually
- renaming should happen only through an authorised Wire Finance IT process
- the name describes the intended function of the device
- the name does not establish ownership
- the name does not establish management state
- the name does not establish compliance
- the name does not establish trust

The final points are especially important.

### A Name Is a Label — Not a Security Control

Consider:

```text
WF-PAW-001
```

The name tells us that the device is intended to function as a Privileged Access Workstation.

But naming a computer:

```text
WF-PAW-001
```

does not turn it into a trusted PAW.

The actual state depends on the controls behind it.

```text
Device Name
      ↓
Indicates Intended Function
```

while:

```text
Approved Asset
      +
Approved Classification
      +
Provisioning
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

does not automatically mean:

```text
Trusted Privileged Workstation
```

The same applies to a standard endpoint.

A computer called:

```text
WF-LT-001
```

is not automatically corporate-owned, Intune-managed, compliant, or secure simply because its hostname follows the Wire Finance naming standard.

The principle is:

**a device name tells us what the device is intended to be — not whether it can be trusted.**

### Naming, Ownership, and Management Are Different

Several concepts need to remain separate.

The naming convention answers:

**What is this device intended to be?**

Ownership answers:

**Who controls the physical asset?**

Device identity answers:

**How is this device represented inside the organisation?**

Management answers:

**Is the device under organisational management?**

Compliance answers:

**Does it currently meet the organisation's requirements?**

Conceptually:

```text
Name
↓
Intended Function

Ownership
↓
Asset Authority

Device Identity
↓
Organisational Identity

Management
↓
Operational Control

Compliance
↓
Current Policy State
```

Together, those signals tell us much more than the hostname alone.

### Naming Survives Reassignment

The naming model is designed to work throughout the device lifecycle.

Imagine:

```text
WF-LT-004
```

is assigned to an employee.

Later, that employee leaves Wire Finance.

The laptop is recovered, reviewed, securely reprovisioned, and assigned to another employee.

The user has changed.

But the device may still be:

```text
Standard Corporate Endpoint
```

Its functional classification has not necessarily changed.

This is another reason Wire Finance avoids encoding the employee's identity in the hostname.

```text
Device Identity
        ≠
Assigned User Identity
```

The two are related, but they are not the same thing.

### Naming Authority

Device names should not be chosen arbitrarily by individual employees.

For Wire Finance, naming authority belongs to:

**Wire Finance IT**

IT is responsible for applying and maintaining the standard across the environment.

Consistent naming becomes valuable when devices appear in:

- asset records
- Microsoft Entra ID
- Microsoft Intune
- Microsoft Defender
- support tickets
- security alerts
- investigations
- administrative documentation

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

Even before an administrator or analyst opens the record, the intended function is easier to understand.

### The Sequential Naming Challenge

The Wire Finance design uses sequential names such as:

```text
WF-LT-001
WF-LT-002
WF-LT-003
```

That is useful for asset management because the identifiers are predictable and easy to reference.

However, the naming standard and the provisioning technology are two different things.

Windows Autopilot does not natively generate sequential numbers such as:

```text
001
002
003
```

for each new device.

Autopilot naming templates can instead use values such as:

```text
%SERIAL%
```

or:

```text
%RAND:x%
```

to create device names dynamically.

For example, a technically compatible Autopilot naming approach might look more like:

```text
WF-LT-%RAND:4%
```

rather than:

```text
WF-LT-###
```

But that would create random identifiers rather than the sequential Wire Finance asset numbers defined in this design.

Wire Finance therefore treats:

```text
WF-LT-###
```

as the **organisational naming and asset standard**, not as a promise that Windows Autopilot will generate the sequence automatically.

### What Happened in the First Pilot?

The first Wire Finance Autopilot deployment deliberately did not apply a device-name template.

The deployment profile used:

```text
Apply device name template
=
No
```

As a result, the successfully provisioned Windows VM retained a Windows-generated hostname rather than automatically becoming:

```text
WF-LT-001
```

That outcome was expected.

It also gave the project an important implementation lesson:

```text
Design Standard
        ≠
Native Autopilot Capability
```

The physical/virtual asset can still conceptually represent:

```text
WF-LT-001
```

inside the Wire Finance asset model while the current operating-system hostname remains different until an authorised naming process changes it.

The project should document both states rather than pretending they are the same.

### Naming and Windows Autopilot

Windows Autopilot can participate in device naming, but the naming options available in the platform need to be respected.

Conceptually:

```text
Corporate Device
        ↓
Autopilot Classification
        ↓
Deployment Profile
        ↓
Microsoft Entra Join
        ↓
Intune Enrollment
```

A device-name template can optionally be introduced where its supported naming model meets the business requirement.

For the current Wire Finance pilot, that option remains disabled.

The important distinction is:

```text
Wire Finance Naming Standard
        ↓
Defines how we want assets to be identified

Autopilot Device Name Template
        ↓
Optional technical mechanism with platform limitations
```

We should not redesign the entire asset convention merely because one provisioning feature cannot create sequential values natively.

Instead, later implementation can determine the best controlled method for reconciling the Windows hostname with the authoritative asset naming standard.

### Naming Record

The Wire Finance device naming standard can therefore be summarised as:

```text
Device Naming Standard
WF-<DEVICE-TYPE>-<NUMBER>

Organisation Prefix
WF

Device Type
Functional classification

Numbering
Three-digit sequential asset identifier

Examples
WF-LT-001
WF-PAW-001
WF-TST-001
WF-DC-001
WF-SRV-001
WF-SYNC-001
WF-JUMP-001
WF-LEGACY-001

Naming Authority
Wire Finance IT

Important Limitation
Native Windows Autopilot naming does not provide
the sequential ### numbering model used by the
Wire Finance asset standard.

Security Principle
A device name indicates intended function.
It does not independently establish ownership,
identity, management, compliance, security state,
or trust.
```

### We Can Name the Device — But It Still Needs an Identity

At this point, the naming model tells us how Wire Finance intends to recognise its systems.

```text
WF-LT-001
→ Standard Corporate Endpoint

WF-PAW-001
→ Privileged Access Workstation

WF-TST-001
→ Deployment / Test Endpoint
```

But a hostname is still only a label.

Before Microsoft Entra ID, Intune, Conditional Access, or security tooling can make meaningful decisions about a device, that machine needs an organisational identity.

For the first Wire Finance rollout, the design is cloud-first.

Standard employee endpoints are intended to become:

```text
Corporate-Owned Windows Device
        ↓
Microsoft Entra Joined
        ↓
Managed by Microsoft Intune
```

Hybrid identity will come later when Wire Finance introduces on-premises Active Directory.

The next question is therefore:

**How does a physical Wire Finance device become a recognised Microsoft Entra device identity, and which join model should it use?**