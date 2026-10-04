---
layout: lesson
title: "Acquisition and Asset Registration"
series: wire-finance
parent: device-lifecycle
order: 1
description: "Before a device reaches Autopilot, Wire Finance needs to establish ownership, business purpose, accountability, and an authoritative record for the physical asset."
---

The lifecycle begins before the device has a Microsoft Entra identity.

Before Intune manages it.

Before Windows Autopilot recognises it.

The endpoint first exists as a **physical business asset**.

#### Physical Ownership Comes First

The initial journey is:

```text
Physical Device Received
        ↓
Ownership Confirmed
        ↓
Asset Record Created
        ↓
Purpose Confirmed
        ↓
Provisioning Approved
```

Wire Finance does not define corporate ownership simply because:

```text
Microsoft Entra Device Object Exists
```

Ownership must originate from an approved business and asset-management process.

#### The Asset Register

The Wire Finance Asset Register becomes the authoritative business record for the hardware.

For example:

```text
Asset ID:
WF-AST-0001

Device:
WF-LT-001

Ownership:
Corporate

Purpose:
Standard Employee Endpoint

Intended User:
Olivia Carter

Lifecycle Status:
Received
```

The device may not yet have received its final technical configuration.

But the organisation already knows:

```text
What We Own

Why We Own It

What It Is Intended For

Who Is Accountable for It

What Lifecycle State It Is In
```

#### Asset Identity Is Not Device Identity

There is an important distinction.

```text
Asset Record
      ↓
Business / Physical Identity
```

is different from:

```text
Microsoft Entra Device
      ↓
Digital Organisational Identity
```

The asset exists before the cloud device identity.

That separation becomes especially important later during reassignment, retirement, and disposal.

Deleting a cloud device object does not make the physical asset disappear.

#### What Should Be Recorded?

The initial asset record should establish information such as:

| Information | Purpose |
|---|---|
| **Asset identifier** | Uniquely identify the physical asset |
| **Ownership** | Confirm Wire Finance controls the hardware |
| **Serial information** | Connect the physical hardware to technical records |
| **Device class** | Standard, PAW, test, or another approved class |
| **Business purpose** | Explain why the device exists |
| **Intended user** | Record the planned assignment where known |
| **Lifecycle status** | Track its current business state |
| **Acquisition information** | Support accountability and lifecycle history |

At this stage:

```text
Lifecycle Status:
Received
```

is appropriate.

#### Purpose Matters Before Provisioning

The organisation should know what it intends to build before provisioning starts.

For example:

```text
Standard Employee Endpoint
      ↓
CORPORATE Provisioning Path
```

or:

```text
Privileged Access Workstation
      ↓
PAW Provisioning Path
```

The technical classification comes later.

The business purpose comes first.

#### Acquisition Decision

The lifecycle begins with:

```text
Physical Asset
      ↓
Ownership Established
      ↓
Purpose Established
      ↓
Asset Record Established
      ↓
Approved to Enter Provisioning
```

The governing principle is:

**Wire Finance establishes business ownership and purpose before the device receives its technical identity.**

The physical asset is now accountable.

Next, we need to introduce that hardware to the provisioning system.

**How does a recorded Wire Finance asset become a device Windows Autopilot can recognise and classify?**