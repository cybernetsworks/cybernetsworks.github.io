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

Administrators use devices to perform privileged actions.

Security teams need devices for testing.

Infrastructure services run on their own systems.

And some older technologies may need to exist temporarily without being trusted like normal corporate endpoints.

Before we decide how devices are joined, enrolled, grouped, or secured, we first need to answer two questions:

**What kinds of devices does Wire Finance operate, and who owns them?**

#### Corporate Ownership Comes First

Wire Finance primarily operates **corporate-owned Windows endpoints**.

That gives the organisation control over how devices are:

- Purchased
- Recorded
- Provisioned
- Assigned
- Managed
- Secured
- Monitored
- Retired

Corporate ownership becomes the foundation for later controls such as Microsoft Intune management, endpoint security, compliance evaluation, and controlled provisioning.

The basic journey looks like this:

```text
Approved Company Device
        ↓
Ownership Recorded
        ↓
Provisioned by IT
        ↓
Assigned for a Defined Purpose
        ↓
Managed Through Microsoft Intune
```

![Wire Finance Managed Endpoint Journey]({{ '/assets/images/device-management/managed_endpoint_journey.png' | relative_url }})

The important idea is that a device does not become trusted simply because it appears inside Microsoft Entra ID or Microsoft Intune.

Its corporate status begins with an approved Wire Finance asset and provisioning process.

##### What Does "Corporate-Owned" Actually Mean?

A device being visible in Microsoft Entra ID does not automatically make it a corporate device.

That distinction matters.

A personal laptop may be capable of creating a device record in an organisation's environment without becoming an approved Wire Finance asset.

So we need to separate several different ideas:

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

For Wire Finance, corporate ownership begins with an approved asset and provisioning process.

```text
Approved Device
      ↓
Ownership Recorded
      ↓
Provisioned by IT
      ↓
Management Established
      ↓
Security Controls Applied
      ↓
Compliance Evaluated
```

Only then do we begin to have the signals needed for stronger trust and access decisions later.

This gives us one of the most important principles in the device model:

**device presence does not equal device trust.**

##### Device Ownership Position

Wire Finance does not treat every ownership model equally.

| Ownership Type | Wire Finance Position |
|---|---|
| **Corporate-owned Windows endpoints** | Primary supported model |
| **Corporate-owned privileged workstations** | Supported with enhanced controls |
| **Corporate-owned test devices** | Supported for lab and validation activities |
| **Personal / BYOD Windows devices** | Outside the initial managed fleet |
| **Personally owned privileged devices** | Prohibited |

This gives the project a clear starting point.

The normal Wire Finance endpoint is a company-controlled device.

Privileged administration requires an even more controlled device.

Test systems are allowed, but only for defined lab and validation purposes.

Personally owned devices are not part of the initial managed Windows fleet, and privileged activity is never performed from a personally owned device.

##### Why BYOD Is Outside the Initial Model

Wire Finance is deliberately starting with corporate-owned Windows endpoints.

That does not mean Bring Your Own Device can never exist.

It means we want to understand and secure the controlled corporate-device journey before introducing another ownership model.

Adding BYOD immediately would introduce additional questions around:

```text
Who owns the device?
      ↓
Who can manage it?
      ↓
Which company controls can be enforced?
      ↓
What business data can live on it?
      ↓
Should it influence access decisions?
```

Those are valid questions, but they do not need to be solved during the first endpoint rollout.

The restriction is even stronger for privileged administration:

**a personally owned device cannot become a Wire Finance Privileged Access Workstation.**

Privileged activity requires a company-controlled device with a known provisioning and security history.

##### Device Classifications

Ownership tells us who controls the asset.

Classification tells us **what the device is for**.

Wire Finance defines five functional device classes:

| Device Class | Naming Pattern | Purpose | Ownership |
|---|---|---|---|
| **Standard corporate endpoint** | `WF-LT-###` | Everyday employee productivity | Corporate |
| **Privileged Access Workstation** | `WF-PAW-###` | Dedicated privileged administration | Corporate |
| **Deployment / test endpoint** | `WF-TST-###` | Policy, deployment, compliance, and security testing | Corporate |
| **Infrastructure / server** | `WF-DC-*`, `WF-SRV-*`, `WF-SYNC-*`, `WF-JUMP-*` | Core infrastructure services | Corporate |
| **Isolated legacy system** | `WF-LEGACY-*` | Controlled legacy-risk testing | Lab-controlled / isolated |

Each class exists for a different reason.

That distinction becomes important later when we begin assigning provisioning methods, security controls, device groups, and compliance expectations.

##### Which Devices Matter First?

Wire Finance defines several device classes, but the first endpoint rollout focuses primarily on three:

```text
Standard Corporate Endpoint
Privileged Access Workstation
Deployment / Test Endpoint
```

These are the device types that will matter most when we begin working with Windows Autopilot and Microsoft Intune.

Infrastructure servers and legacy systems remain part of the wider device model, but they follow different provisioning and management paths.

Keeping those paths separate prevents us from treating every Windows system as though it were just another employee laptop.

##### Standard Corporate Endpoint

The standard corporate endpoint is the default productivity device for Wire Finance employees.

Example:

`WF-LT-001`

This device is intended for everyday business activity.

Users such as Olivia Carter, Maya Patel, Ethan Brooks, and Noah Williams would normally work from this type of device.

The current design expects the standard endpoint to be:

```text
Corporate Owned
      +
Windows
      +
Microsoft Entra Joined
      +
Managed by Intune
      +
Protected by Microsoft Defender
      +
Encrypted with BitLocker
      +
Standard User Rights
      +
Compliance Evaluated
```

The standard laptop therefore becomes the normal workspace for an employee.

For example:

```text
Olivia Carter
      ↓
WF-LT-001
      ↓
Standard Corporate Endpoint
      ↓
Normal Employee Productivity
```

It is not designed for privileged administration.

It is not a test device.

And it is not an infrastructure server.

Those responsibilities have separate device classes.

##### Privileged Access Workstation

A **Privileged Access Workstation**, or PAW, exists for a very different reason.

Example:

`WF-PAW-001`

A PAW is a dedicated hardened device used for approved administrative activity.

It is not simply a more powerful version of a normal employee laptop.

It has a different trust purpose.

A normal corporate laptop may be used for:

```text
Email
Teams
Office
Normal Web Use
Everyday Business Activity
```

A PAW is intended for:

```text
Privileged Administration
Sensitive Configuration
Security Administration
Controlled Administrative Activity
```

Wire Finance does not intend PAWs to be used for routine productivity activity.

This pairs directly with the separate identity model designed earlier.

```text
jordan.lee
      ↓
Standard Corporate Endpoint
      ↓
Normal Business Activity
```

while:

```text
adm-jordan.lee
      ↓
Privileged Access Workstation
      ↓
Approved Administrative Activity
```

The separation exists to reduce the chance that ordinary user activity and highly privileged administration happen in the same workspace.

A personally owned computer should never become a Wire Finance PAW.

##### Deployment and Test Endpoint

Wire Finance also needs somewhere to test changes before they reach ordinary employee devices.

That is the role of the deployment and test endpoint.

Example:

`WF-TST-001`

These devices provide a controlled environment for testing areas such as:

- Windows Autopilot
- Microsoft Intune
- Application deployment
- Compliance policies
- Conditional Access
- Microsoft Defender
- Remediation activity
- Failure scenarios

This gives Wire Finance somewhere to answer:

**What happens if we apply this change?**

before the answer affects the wider organisation.

A test endpoint is therefore deliberately separate from a normal employee device.

Its purpose is controlled validation.

Later, test devices can also be deliberately placed into pilot or non-compliant populations without putting a real employee's workstation at unnecessary risk.

##### Infrastructure Devices

Not every Wire Finance device belongs to an employee.

The environment will eventually include infrastructure systems such as:

```text
WF-DC-*    → Domain Controllers
WF-SRV-*   → Member Servers
WF-SYNC-*  → Hybrid Identity Servers
WF-JUMP-*  → Management Jump Servers
```

These are still corporate assets.

But they are **not part of the standard employee provisioning journey**.

For example:

```text
WF-LT-001
      ↓
Employee Endpoint Provisioning
```

does not imply:

```text
WF-DC-001
      ↓
Same Provisioning Model
```

Infrastructure systems provide different services and carry different risks.

They will therefore require their own provisioning and management approach when Wire Finance later introduces on-premises Active Directory and hybrid identity.

##### Isolated Legacy Systems

Wire Finance also reserves a separate classification for controlled legacy-risk testing:

`WF-LEGACY-*`

Example:

`WF-LEGACY-001`

These systems may eventually help us investigate areas such as:

```text
Unsupported Operating Systems
Legacy Authentication
Weak Protocol Exposure
Segmentation
Detection
Risk Management
```

They are deliberately isolated.

A legacy system should not quietly become part of the normal trusted corporate endpoint population simply because it exists inside the lab.

So:

```text
WF-LEGACY-001
        ≠
Trusted Corporate Endpoint
```

unless a very specific controlled test requires otherwise.

This reinforces the same principle we established earlier:

**presence does not equal trust.**

##### Ownership Becomes Security-Relevant

Corporate ownership might initially sound like an asset-management label.

But later in the project, ownership can become one of the signals used when deciding how devices are grouped, targeted, managed, or evaluated.

Conceptually:

```text
Ownership
      ↓
Device Classification
      ↓
Group Membership
      ↓
Policy Targeting
      ↓
Security Controls
      ↓
Potential Access Decisions
```

That means ownership information must be treated carefully.

A user should not be able to make a personal laptop appear to be:

`Company Owned`

simply because they want access to corporate resources.

The ownership status needs to come from an approved Wire Finance process.

##### The Wire Finance Ownership Rule

The ownership principle can therefore be stated simply:

> **A device is considered corporate-owned only when Wire Finance has acquired, approved, or formally brought the device under company ownership and IT management. Registration in Microsoft Entra ID alone does not establish corporate ownership.**

This protects us from a dangerous assumption:

```text
User Registers Personal Laptop
        ↓
Device Appears in Entra
        ↓
Therefore Corporate?
```

No.

Instead, the expected path is:

```text
Asset Authority
      +
Company Ownership
      +
Approved Provisioning
      +
Management Enrolment
      =
Corporate Managed Endpoint
```

##### Who Owns the Device Information?

Different parts of the device record also have different authorities.

| Device Information | Authority |
|---|---|
| **Asset ownership** | IT / Asset Management |
| **Device purpose** | IT + Manager / Security where relevant |
| **Device name** | IT / Endpoint Management |
| **Autopilot classification** | IT / Endpoint Management |
| **PAW designation** | IT + Security |
| **Test-device designation** | IT / Security |
| **Intune enrolment** | Endpoint Management |
| **Device assignment to user** | IT |
| **Compliance status** | Intune |
| **Security / risk state** | Intune / Defender |
| **Retirement decision** | IT + Business Owner |
| **Disposal** | IT / Asset Management |

This prevents device information from becoming arbitrary.

A user does not simply decide that their laptop is now a privileged workstation.

A normal device does not become a test system because someone changes its name.

And a device does not become trusted simply because it appears in a management portal.

Each important decision has an owner.

##### From Device Presence to Device Trust

The full trust journey looks more like this:

```text
DEVICE EXISTS
      ↓
Ownership Established
      ↓
Device Identity Established
      ↓
Management Established
      ↓
Security Controls Established
      ↓
Compliance Evaluated
      ↓
Access Decisions Made
```

Each stage answers a different question.

```text
Does the device exist?
        ↓
Who owns it?
        ↓
What device is it?
        ↓
Who manages it?
        ↓
What security controls are applied?
        ↓
Does it meet our requirements?
        ↓
Should its state influence access?
```

This prevents Wire Finance from treating trust as a single checkbox.

A device may exist but not be managed.

A managed device may not be compliant.

A compliant device may still have a security problem.

Those signals need to be understood separately before they are combined into access decisions later.

##### The Device Model Is Taking Shape

We now know the major device classes Wire Finance expects to support.

```text
Standard Corporate Endpoint
        ↓
Everyday Productivity

Privileged Access Workstation
        ↓
Sensitive Administration

Deployment / Test Endpoint
        ↓
Controlled Testing

Infrastructure Device
        ↓
Core Services

Legacy System
        ↓
Isolated Risk Testing
```

We also know that corporate ownership must come from an approved asset and provisioning process rather than simply from the existence of a cloud device record.

That gives us a foundation for everything that comes next.

But once dozens of devices begin appearing in Microsoft Entra ID, Intune, Defender, and security logs, we need a consistent way to recognise them.

A device name should tell us something useful before we even open its record.

**So how should Wire Finance name its devices so that purpose and function remain clear as the environment grows?**