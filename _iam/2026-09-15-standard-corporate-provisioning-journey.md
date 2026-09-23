---
layout: lesson
title: "Standard Corporate Provisioning Journey"
series: wire-finance
parent: device-identity-provisioning
order: 6
description: "Follow a Wire Finance laptop from acquisition to employee handover as Autopilot, Microsoft Entra ID, Intune, security controls, and compliance turn hardware into a managed corporate endpoint."
---

We have decided what kinds of devices Wire Finance supports.

We have defined how those devices are named.

We know how a corporate endpoint receives a Microsoft Entra device identity.

We have built the device-group architecture.

And we have decided how Windows Autopilot distinguishes a normal corporate endpoint from a Privileged Access Workstation.

Now we can finally put those decisions together.

The question is no longer:

**What should the device model look like?**

It is:

**What actually happens from the moment Wire Finance receives a laptop to the moment an employee can safely begin work?**

That is the **Standard Corporate Provisioning Journey**.

#### Our Pilot Example

We will follow one device through the process:

```text
Employee:
Olivia Carter

Identity:
olivia.carter@wirefinance.com

Device:
WF-LT-001

Device Type:
Standard Corporate Endpoint

Ownership:
Corporate

Autopilot Classification:
CORPORATE

Provisioning Group:
SG-DEVICES-AUTOPILOT-CORPORATE
```

Wire Finance will use a **user-driven Windows Autopilot deployment** for standard corporate endpoints.

The goal is to move:

```text
Approved Corporate Hardware
        ↓
Recognised Device
        ↓
Provisioned Device
        ↓
Managed Device
        ↓
Secured Device
        ↓
Compliant Device
        ↓
Ready for Employee Use
```

with as little manual build activity as possible.

#### The Full Provisioning Journey

![Wire Finance Standard Corporate Provisioning Journey]({{ '/assets/images/standard_corporate_provisioning_journey.png' | relative_url }})

The diagram shows the entire path from acquisition to operational use.

There are sixteen stages:

| Stage | What Happens | Result |
|---|---|---|
| **1. Device acquired** | Wire Finance obtains the endpoint | Corporate asset exists |
| **2. Asset recorded** | Serial number, ownership, purpose and user assignment are recorded | Asset record established |
| **3. Autopilot registered** | Device hardware identity is registered with Windows Autopilot | Autopilot recognises the device |
| **4. Device classified** | Group Tag `CORPORATE` is applied | Correct provisioning path identified |
| **5. Dynamic group evaluated** | Device enters `SG-DEVICES-AUTOPILOT-CORPORATE` | Standard deployment profile can be targeted |
| **6. Deployment profile assigned** | Standard Wire Finance Autopilot profile applies | User-driven corporate deployment defined |
| **7. User powers on** | Device enters Windows OOBE and connects to the internet | Autopilot service contacted |
| **8. User authenticates** | Olivia signs in using her Wire Finance identity | User identity validated |
| **9. Microsoft Entra Join** | Device joins the Wire Finance tenant | Device identity established |
| **10. Intune enrollment** | Automatic MDM enrollment occurs | Management authority established |
| **11. Enrollment Status Page** | Required device and user setup is tracked | Incomplete deployment can be blocked |
| **12. Apps and configuration** | Required applications and corporate settings are delivered | Working environment prepared |
| **13. Security controls** | Endpoint security configuration is applied | Security state established |
| **14. Compliance evaluation** | Intune evaluates device requirements | Compliance state established |
| **15. Operational classification** | Device satisfies the corporate Windows dynamic rule | `SG-DEVICES-WINDOWS-CORPORATE` membership |
| **16. Ready for use** | Final state and assignment are validated | Employee begins normal work |

The important thing is that these stages do not all mean the same thing.

A device can successfully complete one stage and still not be ready for use.

---

#### What Must Exist Before Olivia Powers On the Laptop?

Autopilot does not begin with a completely empty environment.

Several identity, licensing, enrollment, and targeting decisions need to exist before `WF-LT-001` reaches Olivia.

| Requirement | Wire Finance Design |
|---|---|
| **User identity** | Olivia Carter created in Microsoft Entra ID |
| **Licensing** | Appropriate Microsoft 365 E5 services assigned |
| **Automatic Intune enrollment** | Initially enabled for `SG-PILOT-IDENTITY` |
| **Device join permission** | Pilot users permitted to join approved devices |
| **Device ownership** | Corporate |
| **Autopilot registration** | Completed |
| **Group Tag** | `CORPORATE` |
| **Dynamic membership** | `SG-DEVICES-AUTOPILOT-CORPORATE` |
| **Autopilot profile** | `WF-AP-CORPORATE-USER-DRIVEN` |
| **Join type** | Microsoft Entra Joined |
| **User account type** | Standard User |
| **Enrollment Status Page** | `WF-ESP-CORPORATE` |
| **Required apps and policies** | Assigned before deployment where required |

This gives us an important lesson:

**the employee powering on the laptop is not the beginning of the provisioning design.**

A lot of preparation has already happened before they ever see Windows OOBE.

#### Provisioning Objects Need Their Own Names

Two configuration objects are reserved for the standard corporate journey:

```text
Autopilot Deployment Profile:
WF-AP-CORPORATE-USER-DRIVEN

Enrollment Status Page:
WF-ESP-CORPORATE
```

These are **configuration objects**, not Microsoft Entra groups.

That distinction matters.

```text
SG-DEVICES-AUTOPILOT-CORPORATE
        ↓
Identifies the device population
```

while:

```text
WF-AP-CORPORATE-USER-DRIVEN
        ↓
Defines how that population is provisioned
```

and:

```text
WF-ESP-CORPORATE
        ↓
Controls how provisioning progress is presented
and what must complete before normal use
```

A group identifies **who or what should receive something**.

A profile defines **what should actually happen**.

#### Stage 1 — Acquire the Device

The journey starts outside Microsoft Entra ID and Intune.

Wire Finance first obtains an approved corporate Windows device.

At this point:

```text
Laptop Exists
```

But that does not yet mean:

```text
Entra Device Exists

Intune Device Exists

Device Is Compliant

Device Is Trusted
```

It is simply a physical corporate asset.

That continues the distinction we established earlier:

**physical ownership comes before cloud identity.**

#### Stage 2 — Record the Asset

Before cloud provisioning begins, IT records important information about the device.

For `WF-LT-001`:

```text
Purpose:
Standard Corporate Laptop

Ownership:
Wire Finance

Intended User:
Olivia Carter

Naming Class:
WF-LT-###

Autopilot Classification:
CORPORATE
```

The asset record allows Wire Finance to know what the hardware is supposed to become.

This is still different from creating its Microsoft Entra device identity.

```text
Physical Asset Record
        ↓
Administrative Record

Microsoft Entra Device
        ↓
Digital Device Identity
```

The physical asset exists before Microsoft Entra knows anything about it.

#### Stage 3 — Register the Device with Windows Autopilot

The device's hardware identity is then registered with Windows Autopilot.

This allows the Autopilot service to recognise the machine during Windows setup.

Conceptually:

```text
Physical Laptop
      ↓
Hardware Identity Registered
      ↓
Windows Autopilot Recognises Device
```

This still does **not** mean the device has joined Microsoft Entra ID.

Autopilot registration and Microsoft Entra Join are separate stages.

```text
Autopilot Registered
        ≠
Microsoft Entra Joined
```

The first allows the provisioning service to recognise the hardware.

The second later establishes the organisational device identity.

#### Stage 4 — Apply the `CORPORATE` Classification

The device receives:

```text
Group Tag:
CORPORATE
```

which is represented through:

```text
[OrderID]:CORPORATE
```

That classification tells Wire Finance:

**this device should follow the standard employee provisioning path.**

It does not yet tell us whether the completed device will be secure or compliant.

It answers:

```text
What should we build?
```

not:

```text
Can we trust the finished device?
```

#### Stage 5 — Dynamic Group Evaluation

The Group Tag allows the device to satisfy the dynamic rule for:

`SG-DEVICES-AUTOPILOT-CORPORATE`

```text
(device.devicePhysicalIds -any (_ -eq "[OrderID]:CORPORATE"))
```

The chain now becomes:

```text
CORPORATE
      ↓
[OrderID]:CORPORATE
      ↓
Dynamic Rule
      ↓
SG-DEVICES-AUTOPILOT-CORPORATE
```

Now the device belongs to a population that can receive the standard Wire Finance Autopilot deployment profile.

#### Stage 6 — The Deployment Profile

The planned standard profile is:

`WF-AP-CORPORATE-USER-DRIVEN`

Its role is to define the standard employee provisioning experience.

The major design decisions are:

```text
Deployment Mode:
User-Driven

Join Type:
Microsoft Entra Joined

User Account Type:
Standard User

Management:
Microsoft Intune
```

The device has now moved from:

```text
Generic Windows Hardware
```

toward:

```text
Hardware Designated for
Wire Finance Corporate Provisioning
```

The actual profile will be created during the implementation phase.

At this design stage, we are defining what that profile must achieve.

---

#### Stage 7 — Olivia Powers On the Device

Olivia receives `WF-LT-001`.

She powers it on and connects it to the internet.

The device enters the Windows **Out-of-Box Experience**, or OOBE.

Conceptually:

```text
WF-LT-001
      ↓
Power On
      ↓
Internet Connection
      ↓
Windows OOBE
      ↓
Autopilot Service
      ↓
Wire Finance Profile Found
```

Instead of IT manually building the machine one screen at a time, the provisioning service now knows that the device belongs to Wire Finance and which deployment path it should follow.

#### Stage 8 — User Authentication

Olivia signs in with:

```text
olivia.carter@wirefinance.com
```

This is a **user identity**.

The laptop will receive a separate **device identity**.

```text
Olivia Carter
      ↓
User Identity

WF-LT-001
      ↓
Device Identity
```

The user and device become associated during the provisioning journey, but they are not the same security object.

This distinction matters later during authentication, Conditional Access, monitoring, investigations, and lifecycle management.

#### Stage 9 — Microsoft Entra Join

`WF-LT-001` now joins the Wire Finance Microsoft Entra tenant.

The device receives its cloud identity.

```text
WF-LT-001
      ↓
Microsoft Entra Join
      ↓
Wire Finance Device Identity
```

Microsoft Entra Join answers:

**Which organisation owns this device identity for authentication and access purposes?**

But Entra Join alone does not finish the build.

```text
Microsoft Entra Joined
        ≠
Intune Managed

Microsoft Entra Joined
        ≠
Compliant

Microsoft Entra Joined
        ≠
Fully Secured
```

Those states come next.

#### Stage 10 — Automatic Intune Enrollment

Olivia is part of the initial pilot population:

`SG-PILOT-IDENTITY`

The planned automatic MDM enrollment scope is:

```text
MDM User Scope:
Some

Target:
SG-PILOT-IDENTITY
```

Because Olivia belongs to the approved enrollment population, her qualifying device can automatically enroll into Microsoft Intune during the join process.

```text
Microsoft Entra Joined
        +
Eligible Pilot User
        ↓
Automatic Intune Enrollment
```

At this point, Microsoft Intune becomes the endpoint-management authority.

This gives us another important distinction:

```text
Microsoft Entra
      ↓
Device Identity
```

while:

```text
Microsoft Intune
      ↓
Device Management
```

Wire Finance deliberately combines both, but they remain separate controls.

#### Stage 11 — Enrollment Status Page

The planned corporate Enrollment Status Page is:

`WF-ESP-CORPORATE`

The ESP provides visibility into the provisioning process and can prevent normal device use until required components have completed.

Conceptually:

```text
Device Preparation
        ↓
Device Configuration
        ↓
Required Applications
        ↓
User Configuration
        ↓
Ready
```

This creates a much better experience than allowing the employee to reach a desktop while important security or management components are still missing.

But there is a balance.

Wire Finance should only make **genuinely critical requirements** blocking.

If every optional application becomes a hard dependency, one broken application could prevent the entire provisioning journey from completing.

The goal is not:

```text
Everything Must Finish Before Desktop
```

It is:

```text
Everything Required for Safe Business Use
Must Finish Before Desktop
```

#### Stage 12 — Applications and Configuration

Once Intune management has been established, the endpoint begins receiving the desired Wire Finance configuration.

Planned configuration areas include:

| Control Area | Planned Examples |
|---|---|
| **Configuration** | Windows settings, OneDrive, Microsoft Edge and approved corporate settings |
| **Applications** | Microsoft 365 Apps, Teams and approved business applications |
| **Updates** | Windows Update configuration and staged deployment |
| **Local privilege** | Windows LAPS and controlled administrative access |

Examples of applications and settings may eventually include:

```text
Microsoft 365 Apps
Teams
OneDrive Configuration
Microsoft Edge Configuration
Windows Settings
Approved Business Applications
Windows Update Configuration
```

These are **planned Intune implementation objects**.

We have not built all of them yet.

That distinction is important because this chapter defines the desired provisioning model rather than pretending future implementation work has already been completed.

#### Stage 13 — Security Controls

The corporate device also begins receiving its required endpoint-security configuration.

Planned areas include:

```text
Microsoft Defender
BitLocker
Windows Firewall
Attack Surface Reduction
Endpoint Security Baselines
Windows LAPS
```

Again, these controls will be implemented later.

What matters here is understanding where they belong in the provisioning journey.

```text
Device Identity Established
        ↓
Management Established
        ↓
Security Configuration Applied
```

A laptop should not be considered ready simply because the user can log in.

#### Standard User Rights

The standard Autopilot profile will provision the employee as a:

```text
Standard User
```

not:

```text
Local Administrator
```

So Olivia's journey is:

```text
Olivia Carter
      ↓
WF-LT-001
      ↓
Standard Local Rights
```

This directly supports the Wire Finance access principle established much earlier:

**local administrator rights are not granted by default.**

If elevated endpoint activity is required later, it will use controlled administrative mechanisms rather than giving permanent administrator rights to the ordinary employee identity.

#### Stage 14 — Compliance Evaluation

Once the device is managed and the relevant controls are present, Intune can evaluate whether the endpoint satisfies Wire Finance requirements.

Conceptually:

```text
WF-LT-001
      ↓
Compliance Requirements
      ↓
Evaluation
      ↓
Compliant
or
Noncompliant
```

Planned compliance areas include:

```text
Operating System State
Encryption
Security Health
Device Risk
Required Security Configuration
```

This becomes important later when Conditional Access begins using device state as one of its decision signals.

Instead of making access decisions using only:

```text
Username + Password
```

the future model can consider:

```text
Authentication
      +
User Identity
      +
Device Identity
      +
Compliance
      ↓
Access Decision
```

That is a much stronger trust model.

#### Stage 15 — From Provisioning to Operational State

After successful enrollment, `WF-LT-001` can satisfy the operational corporate Windows rule.

That rule checks for:

```text
Windows
      +
Company Ownership
      +
Intune Management
      +
Enabled Device Identity
```

The device can then enter:

`SG-DEVICES-WINDOWS-CORPORATE`

This marks an important transition.

Earlier:

```text
SG-DEVICES-AUTOPILOT-CORPORATE
        ↓
"How should this device be built?"
```

Now:

```text
SG-DEVICES-WINDOWS-CORPORATE
        ↓
"How should this managed device be controlled?"
```

The device has moved from **provisioning intent** into **operational management**.

It may remain a member of both groups.

That is expected because the groups continue to answer different questions.

---

#### Stage 16 — The Ready-for-Use Gate

A successful login is not the definition of a successfully provisioned device.

Before IT considers `WF-LT-001` ready for normal business use, Wire Finance performs a final acceptance check.

| Validation | Requirement |
|---|---|
| **Asset record exists** | Required |
| **Autopilot registration and `CORPORATE` classification correct** | Required |
| **Microsoft Entra Joined** | Required |
| **Intune enrolled** | Required |
| **Expected device identity / name recorded** | Required |
| **Standard user rights confirmed** | Required |
| **Required applications installed** | Required |
| **Core security configuration received** | Required |
| **Defender onboarding confirmed** | Required when implemented |
| **Encryption confirmed** | Required when implemented |
| **Compliance state acceptable** | Required |
| **`SG-DEVICES-WINDOWS-CORPORATE` membership present** | Required |
| **Assigned user confirmed** | Required |

Only after those checks succeed do we reach:

```text
WF-LT-001
      ↓
Identity Established
      +
Management Established
      +
Applications Delivered
      +
Security Controls Applied
      +
Compliance Validated
      ↓
READY FOR BUSINESS USE
```

This is the difference between:

```text
The Laptop Works
```

and:

```text
The Laptop Is Ready for Wire Finance
```

#### What If Provisioning Fails?

A real provisioning model also needs a failure path.

Imagine one stage does not complete.

Perhaps the device fails to enroll into Intune.

Perhaps a required configuration does not arrive.

Perhaps the security state cannot be validated.

The response should not be:

```text
Close Enough
      ↓
Give Laptop to Employee
```

Instead:

```text
Provisioning Failure
       ↓
Do Not Bypass Required Controls
       ↓
Identify Failed Stage
       ↓
Review Autopilot / Entra / Intune State
       ↓
Correct Configuration
       ↓
Retry or Reset / Reprovision
       ↓
Validate Again
```

The principle is straightforward:

**a failed identity, management, or security stage is not solved by handing the employee an unfinished device.**

The device should reach a known and validated state before normal business use.

#### A Naming Problem We Will Need to Solve During Implementation

Our current device naming standard uses sequential names such as:

```text
WF-LT-001
WF-LT-002
WF-LT-003
```

That remains the Wire Finance design standard.

But there is an implementation question we have deliberately not solved yet.

Native Autopilot naming automation works through deployment-profile naming templates rather than maintaining a true organisation-wide sequential asset counter.

The available implementation mechanisms include patterns based on values such as:

```text
%SERIAL%
```

or:

```text
%RAND:x%
```

So when we build the actual Autopilot profile, we will need to decide how the design standard should be implemented.

Possible approaches include:

```text
Option A

Keep:
WF-LT-###

as the asset / naming standard

and apply the final name through
a controlled IT process
```

or:

```text
Option B

Use an Autopilot-native pattern such as:

WF-LT-%RAND:5%
```

or:

```text
Option C

Use a serial-based naming model
```

We are **not changing the naming standard yet**.

The important thing is recognising that design and platform automation do not always map perfectly onto each other.

That is exactly the kind of issue the implementation phase is supposed to expose.

#### Wire Finance Standard Provisioning Decision

The complete decision can now be recorded as:

```text
Provisioning Method:
Windows Autopilot

Deployment Mode:
User-Driven

Join Type:
Microsoft Entra Joined

Management:
Microsoft Intune

Automatic Enrollment:
Required

Initial MDM Scope:
SG-PILOT-IDENTITY

Autopilot Classification:
CORPORATE

Provisioning Group:
SG-DEVICES-AUTOPILOT-CORPORATE

Autopilot Profile:
WF-AP-CORPORATE-USER-DRIVEN

Enrollment Status Page:
WF-ESP-CORPORATE

User Account Type:
Standard User

Operational Group:
SG-DEVICES-WINDOWS-CORPORATE

Ready-for-Use Requirement:
Successful identity, management, application,
security and compliance validation
```

#### One Journey, Several Different Trust Decisions

The provisioning journey gives us one more useful way to look at device trust.

```text
Acquired
      ↓
We own the hardware.

Registered
      ↓
Autopilot recognises the hardware.

Classified
      ↓
We know which build it should receive.

Entra Joined
      ↓
The device has an organisational identity.

Intune Enrolled
      ↓
The organisation can manage it.

Secured
      ↓
Required endpoint controls are applied.

Compliant
      ↓
The device satisfies defined requirements.

Validated
      ↓
The device is ready for employee use.
```

No single step replaces the others.

That is why:

```text
Corporate Owned
      ≠
Ready for Use
```

and:

```text
Microsoft Entra Joined
      ≠
Ready for Use
```

and even:

```text
Intune Enrolled
      ≠
Ready for Use
```

The full provisioning journey turns those individual states into a managed corporate endpoint.

We now know how a normal employee device should be built from beginning to end.

But `WF-PAW-001` cannot simply follow that same journey with a different name.

A workstation used for privileged administration needs a stronger provisioning and trust model from the beginning.

**So what has to change when the device we are building is a Privileged Access Workstation?**