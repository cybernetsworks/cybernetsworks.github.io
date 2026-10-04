---
layout: lesson
title: "Device Registration"
series: wire-finance
parent: device-lifecycle
order: 2
description: "The asset exists. Now the hardware needs to enter the provisioning system so Windows Autopilot can recognise it and place it on the correct deployment path."
---

The physical asset has been received.

Its ownership and purpose have been recorded.

Now the technical lifecycle begins.

#### From Asset to Provisioning Record

The journey becomes:

```text
Asset Recorded
      ↓
Windows Autopilot Registration
      ↓
Classification Applied
      ↓
Provisioning Population Identified
```

Registration tells the provisioning service:

**this hardware belongs to an organisational deployment process.**

It still does not mean:

```text
Microsoft Entra Joined

Intune Managed

Compliant

Ready for Use
```

Those states come later.

#### Autopilot Registration

For supported corporate Windows devices, the hardware identity is registered with Windows Autopilot.

That creates a provisioning relationship between the device and Wire Finance.

Conceptually:

```text
Physical Device
      ↓
Hardware Identity
      ↓
Windows Autopilot
      ↓
Wire Finance Provisioning Relationship
```

Registration therefore answers a different question from Microsoft Entra Join.

```text
Autopilot Registration
      ↓
How should this hardware be provisioned?
```

while:

```text
Microsoft Entra Join
      ↓
What organisational device identity does it have?
```

#### Classification Comes Next

The registered device can then receive the appropriate Autopilot classification.

For a standard endpoint:

```text
Group Tag:
CORPORATE
```

For a Privileged Access Workstation:

```text
Group Tag:
PAW
```

That classification drives the device toward the correct provisioning population.

For example:

```text
CORPORATE
      ↓
SG-DEVICES-AUTOPILOT-CORPORATE
```

or:

```text
PAW
      ↓
SG-DEVICES-PRIVILEGED
```

The device is now recognised not only as registered hardware, but as hardware with an intended provisioning purpose.

#### Lifecycle Status

During registration and preparation:

```text
Lifecycle Status:
Provisioning
```

becomes appropriate.

The device has moved from simply being received to actively being onboarded.

#### Multiple Records Begin to Appear

At this stage the endpoint may already exist in more than one system.

```text
WF-LT-001
│
├── Asset Register
└── Windows Autopilot
```

Soon it will also appear in:

```text
Microsoft Entra ID
Microsoft Intune
Microsoft Defender for Endpoint
```

Each new record should remain traceable back to the same physical asset.

#### Registration Decision

The lifecycle now looks like:

```text
Corporate Asset
      ↓
Autopilot Registered
      ↓
Correct Classification
      ↓
Correct Provisioning Population
```

The governing principle is:

**registration introduces the device to the provisioning system; it does not establish the final managed or trusted state.**

The device is recognised.

Now it needs to be built.

**What happens when Wire Finance turns registered hardware into a Microsoft Entra joined and Intune-managed endpoint?**