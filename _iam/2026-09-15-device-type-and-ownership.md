---
layout: lesson
title: "Device Types and Ownership"
series: wire-finance
parent: device-identity-provisioning
order: 1
description: "Not every device serves the same purpose. Define the corporate, privileged, pilot, test, and specialist devices Wire Finance will need — and who is responsible for them."
---

Identity is not the only thing Wire Finance needs to control.

Employees work through devices.

Administrators use devices to perform privileged activity.

IT and Security need devices for testing changes safely.

Infrastructure services run on their own systems.

And some older technologies may need to exist temporarily without being treated like normal trusted corporate endpoints.

Before we decide how devices are joined, enrolled, grouped, provisioned, or secured, we first need to answer two questions:

**What kinds of devices does Wire Finance operate, and who owns them?**

### Corporate Ownership Comes First

Wire Finance begins with a simple position:

**the primary managed Windows endpoint is corporate-owned.**

That gives the organisation control over how devices are:

- acquired
- recorded
- provisioned
- assigned
- managed
- secured
- monitored
- recovered
- retired

At a high level:

```text
Approved Company Device
        ↓
Ownership Recorded
        ↓
Purpose Defined
        ↓
Provisioned by IT
        ↓
Assigned to a User or Function
        ↓
Managed Throughout Its Life
```

![Wire Finance Managed Endpoint Journey]({{ '/assets/images/active-directory/managed_endpoint_journey.png' | relative_url }})

Corporate ownership gives Wire Finance an authoritative starting point for later management and security decisions.

But ownership should not be confused with trust.

A device does not become trusted simply because a record for it exists in Microsoft Entra ID or Microsoft Intune.

### Device Presence Does Not Equal Device Trust

Several device concepts need to remain separate.

```text
Device Exists
      ≠
Corporate-Owned
      ≠
Managed
      ≠
Compliant
      ≠
Trusted
```

A personal device may be able to create or appear as a device identity without becoming an approved Wire Finance asset.

Likewise, a corporate-owned device may exist before it has finished provisioning or reached its expected management and security state.

For Wire Finance, the journey starts with an approved asset.

```text
Approved Device
      ↓
Ownership Recorded
      ↓
Provisioned Through an Approved Process
      ↓
Management Established
      ↓
Security Controls Applied
      ↓
Compliance and Risk Evaluated Later
```

This gives us one of the most important principles in the device model:

**device presence does not equal device trust.**

### Device Ownership Position

Wire Finance does not treat every ownership model in the same way.

| Ownership Type | Wire Finance Position |
|---|---|
| **Corporate-owned Windows endpoints** | Primary supported endpoint model |
| **Corporate-owned privileged workstations** | Supported with enhanced controls |
| **Corporate-owned test devices** | Supported for controlled testing and validation |
| **Corporate-owned infrastructure systems** | Supported through infrastructure-specific management |
| **Personal / BYOD Windows devices** | Outside the initial managed Windows fleet |
| **Personally owned privileged devices** | Not permitted for privileged administration |

This gives the project a clear starting boundary.

The normal Wire Finance employee endpoint is company-controlled.

Privileged administration requires an even more controlled device.

Test systems are allowed for defined validation purposes.

Personally owned Windows devices are outside the initial managed fleet.

And privileged administration is never intended to take place from a personally owned device.

### Why BYOD Is Outside the Initial Model

Wire Finance is deliberately starting with corporate-owned Windows endpoints.

That does not mean Bring Your Own Device can never be introduced.

It means the organisation wants to understand and secure the controlled corporate-device journey before introducing another ownership model.

BYOD would create additional questions such as:

```text
Who owns the device?
        ↓
Who is allowed to manage it?
        ↓
Which company controls can be enforced?
        ↓
What organisational data may be stored on it?
        ↓
How should the device influence access decisions?
```

Those are valid design questions.

They simply do not need to be solved during the first Wire Finance endpoint rollout.

The restriction is stronger for privileged administration:

**a personally owned device cannot become a Wire Finance Privileged Access Workstation.**

### Ownership and Classification Are Different

Ownership tells us:

**who controls the asset?**

Classification tells us:

**what is the device for?**

A company may own many different kinds of systems.

Wire Finance therefore separates ownership from functional purpose.

```text
Corporate Ownership
        ↓
What is this device intended to do?
        ↓
Device Classification
```

### Wire Finance Device Classes

Wire Finance defines five broad functional classes.

| Device Class | Example | Purpose | Ownership |
|---|---|---|---|
| **Standard corporate endpoint** | `WF-LT-001` | Everyday employee productivity | Corporate |
| **Privileged Access Workstation** | `WF-PAW-001` | Controlled privileged administration | Corporate |
| **Deployment / test endpoint** | `WF-TST-001` | Endpoint, policy, deployment, and security validation | Corporate |
| **Infrastructure / server** | `WF-DC-001`, `WF-SRV-001`, `WF-SYNC-001` | Core infrastructure services | Corporate |
| **Isolated legacy system** | `WF-LEGACY-001` | Controlled legacy and risk-testing scenarios | Lab-controlled / isolated |

The example names are shown here only to make the classes easier to recognise.

The detailed naming rules belong in the next part of the Device Provisioning Model.

### Standard Corporate Endpoint

The standard corporate endpoint is the normal productivity device used by Wire Finance employees.

Example:

`WF-LT-001`

Users such as Olivia Carter, Maya Patel, Ethan Brooks, and Noah Williams would normally work from this type of endpoint.

Its purpose is everyday business activity.

```text
Employee
        ↓
Standard Corporate Endpoint
        ↓
Normal Productivity
```

The standard corporate endpoint is intended to become:

- company-owned
- Microsoft Entra joined
- managed through Microsoft Intune
- protected through Wire Finance endpoint-security controls
- evaluated against the organisation's compliance requirements
- operated by the employee using standard user rights

Those controls will be defined and implemented later.

At this stage, the important point is its purpose:

**this is the normal employee workspace.**

It is not intended to be a privileged workstation, infrastructure server, or dedicated test system.

### Privileged Access Workstation

A Privileged Access Workstation, or PAW, exists for administrative activity that carries greater risk.

Example:

`WF-PAW-001`

Its purpose is fundamentally different from a standard employee laptop.

```text
Standard Endpoint
        ↓
Everyday Productivity

Privileged Access Workstation
        ↓
Controlled Administration
```

A PAW is corporate-owned and designed for a more tightly controlled administrative purpose.

It is not simply a standard laptop with a more powerful user account.

The detailed PAW provisioning journey, restrictions, and security expectations are covered later in the Device Provisioning Model.

### Deployment and Test Endpoint

Wire Finance also needs somewhere to test endpoint changes before those changes reach normal employee devices.

Example:

`WF-TST-001`

The purpose of this class is controlled validation.

It may be used to test areas such as:

- Windows Autopilot
- Microsoft Intune configuration
- application deployment
- compliance behaviour
- endpoint-security settings
- remediation
- failure scenarios

Conceptually:

```text
New Endpoint Change
        ↓
Test Device
        ↓
Observe
        ↓
Validate
        ↓
Refine
        ↓
Pilot / Wider Deployment
```

A deployment or test endpoint exists so Wire Finance can answer:

**What happens if we apply this change?**

before the answer affects a real employee's normal workstation.

The detailed pilot and testing model is covered later.

### Infrastructure Devices

Not every Wire Finance device belongs to an employee.

The environment will eventually contain systems such as:

- domain controllers
- member servers
- hybrid identity servers
- management systems

Examples include:

```text
WF-DC-001
WF-SRV-001
WF-SYNC-001
WF-JUMP-001
```

These are corporate assets, but they are **not part of the normal employee endpoint-provisioning journey**.

A standard laptop and a domain controller may both run Windows, but they have completely different purposes and risk profiles.

```text
WF-LT-001
        ↓
Employee Endpoint

WF-DC-001
        ↓
Infrastructure System
```

Infrastructure systems will receive their own design and implementation treatment when Wire Finance introduces on-premises Active Directory and hybrid identity.

### Isolated Legacy Systems

Wire Finance also reserves a separate class for controlled legacy-risk testing.

Example:

`WF-LEGACY-001`

These systems may eventually help the project explore areas such as:

- unsupported operating systems
- legacy authentication
- weak protocol exposure
- segmentation
- detection
- risk management

Their defining characteristic is **isolation**.

A legacy system should not quietly become part of the normal trusted corporate endpoint population simply because it exists inside the lab.

```text
WF-LEGACY-001
        ≠
Normal Corporate Endpoint
```

Its presence is intentional, but so is its separation.

### Which Device Classes Matter First?

Although the wider design contains five classes, the first endpoint rollout focuses primarily on:

```text
Standard Corporate Endpoint
        ↓
Employee Productivity

Privileged Access Workstation
        ↓
Administrative Activity

Deployment / Test Endpoint
        ↓
Controlled Validation
```

These are the classes most directly connected to Microsoft Entra ID, Microsoft Intune, and Windows Autopilot during the initial endpoint build.

Infrastructure and legacy systems remain part of the overall architecture, but they follow different provisioning paths.

This prevents Wire Finance from treating every Windows system as though it were just another employee laptop.

### Ownership Needs an Authoritative Record

A device should not become corporate-owned simply because a user says it is.

Wire Finance therefore needs an authoritative record of the physical asset and its assignment.

For the initial environment, the asset register provides that business record.

It records information such as:

- the device asset
- company ownership
- intended purpose
- assigned user
- device class
- lifecycle status

This gives us an important distinction:

```text
Asset Register
        ↓
Who owns the physical device
and who is it assigned to?

Microsoft Entra ID
        ↓
What is the device's cloud identity?

Microsoft Intune
        ↓
How is the device managed?

Microsoft Defender
        ↓
What security activity and risk do we see?
```

No single system tells the entire device story.

The asset register remains the authoritative record for physical ownership and assignment, while the Microsoft platforms provide identity, management, compliance, and security information.

### The Wire Finance Ownership Rule

The ownership principle can therefore be stated simply:

> **A device is considered corporate-owned only when Wire Finance has acquired, approved, or formally brought it under company ownership and its asset status is recorded through an authorised process. A cloud device record alone does not establish corporate ownership.**

This protects Wire Finance from a dangerous assumption:

```text
User Registers Device
        ↓
Device Appears in Microsoft Environment
        ↓
Therefore Corporate-Owned?
```

No.

The expected relationship is closer to:

```text
Approved Asset
        +
Company Ownership
        +
Authorised Provisioning
        +
Management
        =
Corporate Managed Endpoint
```

### Who Owns Device Decisions?

Different parts of the device record have different authorities.

| Device Information | Authority |
|---|---|
| **Physical asset ownership** | Wire Finance IT / asset record |
| **Device purpose** | IT, with business or Security input where required |
| **Device classification** | IT |
| **Privileged workstation designation** | IT + Security |
| **Test-device designation** | IT / Security |
| **Device assignment to user** | IT |
| **Provisioning classification** | IT |
| **Management state** | Microsoft Intune |
| **Compliance state** | Microsoft Intune |
| **Security / risk state** | Intune and Microsoft Defender |
| **Reassignment or retirement decision** | IT with business input where required |
| **Disposal** | IT through the approved asset process |

This prevents device information from becoming arbitrary.

A user cannot decide that a personal laptop is now a corporate device.

A standard laptop does not become a PAW because somebody renames it.

And a device does not become trusted simply because it appears in a Microsoft portal.

Important device decisions need an owner.

### The Device Model Is Taking Shape

We now have two important pieces of the device model.

First, ownership:

```text
Corporate
        or
Outside Initial Managed Fleet
```

Second, classification:

```text
Standard Corporate Endpoint
        ↓
Everyday Productivity

Privileged Access Workstation
        ↓
Controlled Administration

Deployment / Test Endpoint
        ↓
Controlled Validation

Infrastructure Device
        ↓
Core Services

Legacy System
        ↓
Isolated Risk Testing
```

That gives Wire Finance a consistent way to understand what a device is before we begin provisioning it.

But as the environment grows, those devices will appear in asset records, Microsoft Entra ID, Intune, Defender, logs, and investigations.

We therefore need a consistent way to recognise them.

The next question is:

**How should Wire Finance name its devices so that purpose remains clear as the environment grows?**