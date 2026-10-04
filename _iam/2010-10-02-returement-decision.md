---
layout: lesson
title: "Retirement Decision"
series: wire-finance
parent: device-lifecycle
order: 9
description: "Retirement starts with a business decision, not a Delete button. Wire Finance first decides the future of the physical asset, then selects the correct technical offboarding actions."
---

Eventually, every device reaches a point where Wire Finance no longer wants it in active service.

Possible reasons include:

```text
Hardware Age

Unsupported Operating System

Repeated Hardware Failure

Replacement Programme

Security Requirements

Loss or Theft

Device No Longer Required
```

At that point, the organisation makes a retirement decision.

#### Retirement Pending

The lifecycle changes to:

```text
Lifecycle Status:
Retirement Pending
```

This state means:

```text
Decision Made
        +
Cleanup Still Required
```

The device should no longer be treated as an ordinary In Service endpoint.

But its lifecycle is not complete yet.

#### Retirement Is a Business Decision

Retirement answers:

**Should this device continue operating inside Wire Finance?**

If the answer is no:

```text
In Service / Security Hold / Repair
        ↓
Retirement Approved
        ↓
Data and Ownership Reviewed
        ↓
Correct Removal Actions Selected
```

The technical actions follow the decision.

They do not create the decision.

#### What Is Happening to the Physical Hardware?

This is the key question.

```text
Is the Hardware Staying
Inside Wire Finance?
```

If:

```text
YES
```

we may actually be dealing with:

```text
Reassignment
Reset
Reprovisioning
```

rather than true retirement.

If:

```text
NO
```

then the device is leaving organisational service and needs full decommissioning.

#### Reset, Retire, Wipe, Delete, and Autopilot Removal

These actions mean different things.

| Action | Meaning in the Wire Finance Model | Typical Use |
|---|---|---|
| **Autopilot Reset / controlled reprovisioning** | Return a managed endpoint to an approved reusable corporate state while retaining the provisioning relationship where appropriate | Internal reassignment / reuse |
| **Retire** | Remove organisational management and corporate configuration/data as part of removing the device from managed service | Device leaving organisational management |
| **Wipe** | Strong reset that removes local device data and settings | Rebuild, transfer, security recovery, or disposal |
| **Delete** | Remove a technical management or directory record after the lifecycle decision has been made | Technical record cleanup |
| **Remove Autopilot registration** | Remove the hardware from Wire Finance provisioning ownership | Permanent departure from Wire Finance |

So:

```text
DELETE
      ≠
RETIREMENT PLAN
```

and:

```text
WIPE
      ≠
AUTOPILOT REMOVAL
```

The correct action depends on what is happening to the actual asset.

#### Device Staying Inside Wire Finance

Suppose an employee leaves but the laptop remains healthy.

That is not automatically retirement.

```text
User Leaves
      ↓
Laptop Remains Useful
      ↓
Wire Finance Keeps Hardware
      ↓
Reset / Reprovision
      ↓
Reassign
```

The Autopilot relationship may remain because the hardware still belongs to Wire Finance.

#### Device Permanently Leaving Wire Finance

Now consider:

```text
Old Laptop
      ↓
Approved for Sale / Transfer / Disposal
```

The decision is different.

```text
Retirement Approved
        ↓
Required Business Data Preserved
        ↓
Secure Reset / Wipe
        ↓
Technical Relationships Removed
        ↓
Provisioning Relationship Removed
        ↓
Asset Record Closed
        ↓
Physical Device Released
```

This is the point where full decommissioning becomes necessary.

#### Retirement Decision

The governing principle is:

**retirement begins by deciding the future of the physical asset; technical cleanup is then selected to match that decision.**

The device has been approved to leave service.

But several records and relationships may still exist.

**How does Wire Finance make sure the device is actually gone from every system — without losing the historical evidence that it once existed?**