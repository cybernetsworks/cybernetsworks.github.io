---
layout: lesson
title: "Device Lifecycle Overview"
series: wire-finance
parent: device-identity-provisioning
order: 10
nav_id: device-lifecycle
children_heading: "Explore the Device Lifecycle"
description: "Follow a Wire Finance device through its complete managed lifecycle, from acquisition and provisioning to reassignment, security response, retirement, and final disposal."
---

Up to this point, most of our device design has answered one question:

**How does Wire Finance get a device into service?**

We have defined how devices are:

- Owned
- Named
- Classified
- Joined
- Provisioned
- Managed
- Secured
- Tested
- Evaluated for compliance

But provisioning is only the beginning.

A device may spend several years inside the organisation.

During that time it may be updated, repaired, reassigned, placed into a pilot, investigated after a security event, repurposed, retired, or eventually disposed of.

So the final part of the device model asks:

**What happens to a device from the moment Wire Finance acquires it until the physical asset permanently leaves the organisation?**

The core principle is:

**a managed device has a lifecycle, not just an enrollment event.**

#### The Complete Device Journey

The Wire Finance device lifecycle is shown below.

![Wire Finance Device Lifecycle Overview]({{ '/assets/images/device-management/device_lifecycle_overview.png' | relative_url }})

The lifecycle can be understood as three broad phases:

```text
ENTER SERVICE
      ↓
Acquire
      ↓
Register
      ↓
Provision
      ↓
Validate


OPERATE
      ↓
In Service
      ↓
Maintain
      ↓
Repair / Reassign / Security Response
      ↓
Return to Service Where Appropriate


LEAVE SERVICE
      ↓
Retirement Decision
      ↓
Decommission
      ↓
Dispose
```

But the journey is not always a straight line.

A device might move:

```text
In Service
    ↓
Repair
    ↓
In Service
```

or:

```text
In Service
    ↓
Reassignment
    ↓
In Service
```

or:

```text
In Service
    ↓
Security Hold
    ↓
Investigate
   /         \
Recover      Retire
```

Lifecycle management therefore means controlling both the **normal path** and the **exceptions**.

#### Lifecycle Stages

| Stage | Purpose | Primary System / Owner |
|---|---|---|
| **Acquisition** | Establish ownership, purpose, and accountability | Wire Finance Asset Register / IT |
| **Registration** | Introduce the device to the approved provisioning service | Windows Autopilot / Endpoint Management |
| **Provisioning** | Build the approved Windows configuration and establish cloud identity and management | Autopilot + Entra ID + Intune |
| **Validation** | Confirm management, security, applications, and compliance readiness | Intune + Defender + IT / Security |
| **Operation** | Normal business or approved privileged use | Intune + Defender + Entra ID |
| **Maintenance** | Apply updates, repairs, remediation, and approved configuration changes | Endpoint Management / IT |
| **Reassignment** | Prepare the device for another approved user or business purpose | Intune + Asset Register |
| **Security Hold** | Control a lost, stolen, high-risk, or suspected compromised device | Security + Defender + Intune + Entra ID |
| **Retirement** | Approve removal from active organisational service | IT + Business Owner |
| **Decommission / Disposal** | Remove remaining technical associations and complete the physical lifecycle | IT + Asset Management + Security |

Each stage answers a different lifecycle question.

Together, they create the complete journey.

#### One Device, Several Records

Consider:

`WF-LT-001`

That single physical device may have records in:

```text
WF-LT-001
│
├── Wire Finance Asset Register
├── Windows Autopilot
├── Microsoft Entra ID
├── Microsoft Intune
└── Microsoft Defender for Endpoint
```

Those records are not duplicates.

They represent different responsibilities.

| Record | Question It Answers |
|---|---|
| **Asset Register** | What physical asset do we own, who has it, and what is its purpose? |
| **Windows Autopilot** | What provisioning relationship does the hardware have? |
| **Microsoft Entra ID** | What is the organisational device identity? |
| **Microsoft Intune** | Is the endpoint managed, configured, and compliant? |
| **Microsoft Defender for Endpoint** | What is its current security and risk state? |

Lifecycle management keeps these different views aligned.

#### Lifecycle Statuses

Wire Finance also records the current business state of the physical asset.

| Status | Meaning |
|---|---|
| `Received` | Device has physically arrived and been recorded |
| `Provisioning` | Device is being registered, configured, and validated |
| `In Service` | Device is approved for normal operational use |
| `Pilot/Test` | Device is temporarily allocated to controlled rollout or lab activity |
| `Repair` | Device is under technical or hardware repair |
| `Reassignment` | Device is being prepared for another user or purpose |
| `Security Hold` | Device is lost, stolen, high-risk, or under security investigation |
| `Retirement Pending` | Device has been approved to leave service but cleanup is incomplete |
| `Retired` | Device is no longer operational within Wire Finance |
| `Disposed` | The physical lifecycle has been completed |

A Microsoft Entra device object cannot tell us all of this.

That is why the Asset Register remains part of the design.

#### Lifecycle Ownership

Responsibility also moves as the device moves.

| Lifecycle Activity | Primary Owner |
|---|---|
| **Purchase / receipt / asset registration** | IT / Asset Management |
| **Autopilot registration and provisioning** | Endpoint Management |
| **Security and compliance validation** | IT / Security |
| **User assignment and reassignment** | IT / Endpoint Management |
| **Daily endpoint management** | Endpoint Management |
| **Security monitoring and incident response** | SOC / Security |
| **Repair** | IT / approved vendor |
| **Retirement approval** | IT + Business Owner |
| **Cloud and provisioning cleanup** | IT / Identity / Endpoint Management |
| **Physical disposal** | IT / Asset Management |

No single portal or team owns the entire device story.

The lifecycle works because those responsibilities remain coordinated.

#### From Lifecycle Map to Lifecycle Decisions

The diagram gives us the complete journey.

Now we can examine what each stage actually means.

We will begin before Microsoft Entra ID, Intune, and Autopilot are involved at all.

**What makes a newly received laptop an accountable Wire Finance business asset before technical provisioning even begins?**