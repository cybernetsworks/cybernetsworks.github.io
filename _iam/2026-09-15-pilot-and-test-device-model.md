---
layout: lesson
title: "Pilot and Test Device Model"
series: wire-finance
parent: device-identity-provisioning
order: 8
description: "Pilot devices prove that a change works safely. Test devices deliberately exercise controlled failure paths. Wire Finance separates the two so new configurations can be validated without turning production endpoints into experiments."
---

Wire Finance now has provisioning models for two very different endpoint types.

A standard corporate laptop is designed for everyday employee productivity.

A Privileged Access Workstation is designed for controlled administrative activity.

But before new applications, policies, security controls, or compliance requirements reach the wider organisation, we need somewhere to prove that they behave as expected.

That introduces two additional device roles:

**pilot devices**

and:

**test devices**

They may sound similar.

They are not.

A pilot device asks:

**Can we safely introduce this change into production?**

A test device asks:

**Does the control behave correctly when we deliberately exercise a failure path?**

That distinction forms the foundation of the Wire Finance endpoint testing model.

### Pilot and Test Are Different Jobs

Wire Finance separates **controlled rollout** from **controlled failure testing**.

![Wire Finance Pilot vs Test Device Model]({{ '/assets/images/active-directory/pilot_vs_test_device_model.png' | relative_url }})

A pilot follows this general path:

```text
Candidate Change
      ↓
Pilot Device
      ↓
Normal Operation
      ↓
Observe and Validate
      ↓
Approve, Refine, or Reject
      ↓
Production Rollout
```

A test device follows a different path:

```text
Known-Good Device
      ↓
Approved Test Scenario
      ↓
Controlled Failure Condition
      ↓
Observe Expected Behaviour
      ↓
Remediate
      ↓
Return to Known-Good State
```

Both approaches make change safer.

But they answer different questions.

```text
PILOT
=
Can we safely deploy this?
```

while:

```text
TEST
=
Does the control behave correctly
when something goes wrong?
```

### Pilot vs Noncompliance Test

Wire Finance uses two dedicated groups.

| Area | Pilot Device | Noncompliance Test Device |
|---|---|---|
| **Primary Group** | `SG-DEVICES-WINDOWS-PILOT` | `SG-DEVICES-NONCOMPLIANT-TEST` |
| **Membership Type** | Assigned | Assigned |
| **Purpose** | Validate candidate changes under normal working conditions | Validate controlled failure, enforcement, detection, and recovery behaviour |
| **Typical Device** | Selected managed corporate endpoint or dedicated pilot endpoint | Dedicated test endpoint such as `WF-TST-001` |
| **Normal Business Use** | May continue where the change is considered safe enough | Avoided during disruptive testing |
| **Base Provisioning** | Normally `CORPORATE` | Normally `CORPORATE` |
| **Expected Outcome** | Candidate change behaves safely enough for wider deployment | Failure occurs as expected and the device can be restored |
| **Exit Action** | Retain or remove from pilot after rollout decision | Restore, validate, and remove from test group |

The difference is not whether the device is corporate-owned.

Both can begin from the normal corporate device model.

The difference is **what Wire Finance is temporarily asking the device to do**.

### Why Both Groups Use Assigned Membership

Both groups use:

```text
Membership Type
=
Assigned
```

This is deliberate.

A device should not enter a pilot population merely because an unrelated attribute happened to match a dynamic rule.

More importantly, a production endpoint should never enter a disruptive testing population automatically because it unexpectedly became noncompliant.

Membership represents an **explicit operational decision**.

```text
Selected for Controlled Rollout
        ↓
SG-DEVICES-WINDOWS-PILOT
```

and:

```text
Approved for Controlled Failure Testing
        ↓
SG-DEVICES-NONCOMPLIANT-TEST
```

This follows a principle already used elsewhere in the Wire Finance architecture:

**where membership represents a deliberate decision, use deliberate assignment.**

### The Pilot Device Model

Pilot devices are used for **staged rollout**.

They receive a candidate configuration before the wider corporate population so IT can observe how that change behaves under realistic conditions.

A pilot device normally remains:

```text
Corporate Owned
      +
Microsoft Entra Joined
      +
Intune Managed
      +
Security Controlled
      +
Used Normally
```

The difference is that it receives selected changes **earlier**.

Those changes may eventually include:

- an Intune configuration profile
- an application
- a Windows configuration
- an update policy
- an endpoint-security setting
- a compliance-policy change
- a remediation
- another managed endpoint change

The purpose is not to break the machine.

The purpose is to discover whether the change behaves correctly before exposing a wider population to it.

### What Are We Validating During a Pilot?

A successful pilot means more than:

```text
The Policy Applied
```

Wire Finance also needs to understand whether the endpoint remains usable.

Questions might include:

- Did the configuration apply correctly?
- Does the device remain usable?
- Did an important business function stop working?
- Are required applications still compatible?
- Did management continue to function?
- Did the change create unexpected user impact?
- Did the intended security behaviour occur?
- Was anything unexpected observed?

A configuration can deploy successfully and still be a poor production change.

For example:

```text
Configuration Applied Successfully
        +
Critical Business Function Breaks
        =
Pilot Failed
```

The purpose of the pilot is to discover that **before** the wider organisation does.

### Pilot Rollout Flow

A typical Wire Finance pilot follows this sequence:

| Stage | Pilot Action |
|---|---|
| **1. Change prepared** | IT defines the candidate configuration and expected result |
| **2. Pilot scope selected** | Approved devices are added to `SG-DEVICES-WINDOWS-PILOT` |
| **3. Candidate change assigned** | The configuration targets the pilot population |
| **4. Normal operation observed** | Usability, compatibility, management health, and expected behaviour are reviewed |
| **5. Results evaluated** | Problems are corrected, the change is rejected, or it is approved |
| **6. Production rollout** | The validated change targets the intended production population |
| **7. Pilot membership reviewed** | Devices remain in the standing pilot only where that continues to serve a purpose |

Conceptually:

```text
Prepare
   ↓
Pilot
   ↓
Observe
   ↓
Refine if Necessary
   ↓
Approve
   ↓
Production
```

The pilot population creates a controlled buffer between:

```text
New Change
```

and:

```text
Production Fleet
```

### Pilot Membership Is an Overlay

Pilot membership does not replace the device's normal operational classification.

For example:

```text
WF-LT-001
│
├── SG-DEVICES-WINDOWS-CORPORATE
│      ↓
│   Normal Corporate Endpoint
│
└── SG-DEVICES-WINDOWS-PILOT
       ↓
    Receives Selected Changes First
```

There is no contradiction.

The corporate group answers:

```text
What is this device operationally?
```

The pilot group answers:

```text
Should this device receive
candidate changes before production?
```

One device can therefore belong to both groups because the memberships describe different relationships.

### The Test Device Model

Controlled failure testing serves a different purpose.

Wire Finance uses:

`SG-DEVICES-NONCOMPLIANT-TEST`

for devices explicitly approved for controlled compliance, enforcement, or security-response testing.

The preferred dedicated endpoint is:

`WF-TST-001`

The goal is not to use normal employee endpoints as disposable experiments.

Instead, Wire Finance begins from a known managed state and deliberately introduces an approved test condition.

```text
Known-Good Corporate State
        ↓
Approved Test
        ↓
Controlled Failure Condition
        ↓
Observe
        ↓
Remediate
        ↓
Validate Recovery
        ↓
Known-Good State
```

The principle is:

**a test device deliberately exercises the failure path.**

### Why the Test Device Still Uses the Corporate Base Build

Wire Finance does not currently need an Autopilot Group Tag called:

```text
TEST
```

`WF-TST-001` can begin with the normal:

```text
Autopilot Classification
=
CORPORATE
```

and receive the standard corporate provisioning foundation.

When a controlled test is required, it can then be explicitly added to:

`SG-DEVICES-NONCOMPLIANT-TEST`

The resulting model is:

```text
CORPORATE Base Build
        ↓
Known Managed State
        ↓
Explicit Test Assignment
        ↓
Controlled Test Scenario
```

This is useful because many tests ask:

**What happens when a normally managed corporate endpoint stops meeting one of our requirements?**

Starting from a known corporate state gives Wire Finance a meaningful baseline for comparison.

### Test Status Is an Operational Overlay

The word:

```text
TEST
```

does not currently describe a fundamentally different provisioning architecture.

It describes what Wire Finance is temporarily doing with the device.

So:

```text
Provisioning Classification
        ↓
CORPORATE
```

can coexist with:

```text
Operational Purpose
        ↓
Controlled Test
```

Wire Finance uses a device group for that temporary operational purpose instead of creating another permanent provisioning class.

### Why There Is No PILOT or TEST Autopilot Tag

Autopilot Group Tags are reserved for materially different **base provisioning paths**.

The current Wire Finance classifications are:

```text
CORPORATE
PAW
```

Those represent different intended provisioning models.

Pilot and test roles do not.

```text
PILOT
        ↓
Receive Candidate Changes First
```

and:

```text
TEST
        ↓
Approved for Controlled Test Activity
```

Neither means:

```text
Build a Fundamentally Different
Windows Device From Scratch
```

Wire Finance therefore does not currently create additional `PILOT` or `TEST` Group Tags.

This keeps the Autopilot model simple and prevents temporary operational activities from becoming permanent provisioning classes.

### Controlled Test Flow

A controlled noncompliance test follows a defined lifecycle.

| Stage | Test Action |
|---|---|
| **1. Known-good state confirmed** | Verify that the endpoint begins in its expected managed state |
| **2. Test objective defined** | Record what is being tested and what success should look like |
| **3. Test approved** | IT or Security approves the scope and expected impact |
| **4. Device assigned** | The dedicated endpoint is added to `SG-DEVICES-NONCOMPLIANT-TEST` |
| **5. Controlled condition introduced** | An approved configuration causes the selected requirement to fail |
| **6. Behaviour observed** | Relevant management and security behaviour is reviewed |
| **7. Device remediated** | The approved failure condition is removed or corrected |
| **8. Recovery validated** | The endpoint is confirmed to have returned to the expected state |
| **9. Test membership removed** | The device leaves the test group unless another approved test follows |

The beginning and the end are just as important as the failure in the middle.

### Failure Must Be Intentional

There is an important difference between:

```text
Device Unexpectedly Becomes Noncompliant
```

and:

```text
Device Deliberately Placed Into
an Approved Test Scenario
```

Those are not the same event.

An unexpected production failure is:

```text
Operational or Security Issue
        ↓
Investigate
        ↓
Remediate
```

An approved lab test is:

```text
Defined Objective
        ↓
Controlled Failure
        ↓
Observe Expected Response
        ↓
Restore
```

This means:

**a noncompliant device does not automatically become a test device.**

That distinction prevents genuine operational problems from being mistaken for authorised lab activity.

### Test Success Can Include Failure

Pilot and test success also mean different things.

For a pilot, success generally means:

```text
Desired Change Worked
        +
Nothing Important Broke
```

For a controlled test, success may deliberately include:

```text
Expected Requirement Failed
        +
Expected Control Responded
        +
Evidence Was Produced
        +
Remediation Worked
        +
Device Returned to Known-Good State
```

The failure condition itself may therefore be part of a **successful test**.

What matters is whether the environment behaved as designed and whether Wire Finance could safely recover it.

The detailed compliance, risk, Conditional Access, and Defender behaviours are covered in the next part of the Device Provisioning Model.

### One Device Can Belong to Several Groups

`WF-TST-001` gives us a useful example of intentional group overlap.

Depending on what Wire Finance is doing with it, the device might belong to:

| Group | What the Membership Represents |
|---|---|
| `SG-DEVICES-AUTOPILOT-CORPORATE` | Standard corporate provisioning intent |
| `SG-DEVICES-WINDOWS-CORPORATE` | Current managed corporate operational state |
| `SG-DEVICES-WINDOWS-PILOT` | Receives selected candidate configurations first |
| `SG-DEVICES-NONCOMPLIANT-TEST` | Explicitly approved for a controlled test scenario |

Those memberships describe different dimensions of the same device.

```text
Provisioning Intent
        +
Operational State
        +
Pilot Role
        +
Testing Role
```

There is no requirement for one device to belong to only one device group.

What matters is that every membership has a clear reason.

### Production Devices Are Not Lab Equipment

Wire Finance needs a firm boundary around disruptive testing.

Normal production devices should not be casually added to:

`SG-DEVICES-NONCOMPLIANT-TEST`

If an existing production endpoint is going to become a dedicated lab device, the change should be deliberate.

```text
Production Endpoint
        ↓
Lab Requirement Identified
        ↓
Purpose Reviewed
        ↓
Device Deliberately Reclassified
        ↓
Approved Test Activity
```

This protects normal employee endpoints from experimental conditions that could interfere with business activity.

### Privileged Workstations Stay Out of Ordinary Tests

`WF-PAW-###` devices have a different security purpose.

They should not be casually placed into ordinary pilot or failure-testing exercises.

A PAW-related test would require its **own explicitly approved privileged-device scenario**.

The boundary is:

```text
PAW
   ↓
Privileged Administration
```

versus:

```text
Test Endpoint
      ↓
Controlled Validation
```

A device should not casually drift between those purposes.

### Every Test Needs an Exit Plan

Before introducing a controlled failure, Wire Finance should know:

```text
What are we testing?

What result do we expect?

Which device is involved?

What could be affected?

What evidence do we need?

How will the device be restored?

How will recovery be validated?

When does the test end?
```

A controlled test without a recovery plan is not really controlled.

The purpose is to learn from failure behaviour without losing control of the endpoint used to create it.

### Special-Purpose Membership Has a Lifecycle

Because both pilot and test groups use Assigned membership, those memberships also need to be managed deliberately.

For a pilot:

```text
Selected
   ↓
Pilot
   ↓
Validated
   ↓
Rollout Decision
   ↓
Retain or Remove
```

For a test:

```text
Approved
   ↓
Tested
   ↓
Restored
   ↓
Validated
   ↓
Remove
```

Devices should not quietly remain in special-purpose groups after that purpose has ended.

Otherwise, today's temporary experiment becomes tomorrow's unexplained policy assignment.

### Wire Finance Pilot and Test Decision

The current design can therefore be recorded as:

```text
PILOT

Group:
SG-DEVICES-WINDOWS-PILOT

Membership:
Assigned

Purpose:
Controlled staged rollout and validation
before wider deployment

Base Provisioning:
Normally CORPORATE

Exit:
Retain only as part of the approved
pilot population, otherwise remove
```

and:

```text
TEST

Group:
SG-DEVICES-NONCOMPLIANT-TEST

Membership:
Assigned

Primary Test Endpoint:
WF-TST-001

Base Provisioning:
CORPORATE

Purpose:
Controlled validation of failure,
enforcement, monitoring, remediation,
and recovery

Production Protection:
No automatic test membership

Exit:
Restore known-good state,
validate recovery,
then remove test membership
```

The governing principle is:

**pilot devices prove that a change works safely; test devices deliberately exercise an approved failure path. Neither role replaces the device's corporate ownership, identity, management state, or provisioning model.**

### From Testing to Device Security

At this point, Wire Finance can:

- provision standard corporate devices
- provision privileged workstations
- stage changes through pilot devices
- exercise controlled failure paths through test devices

But those activities depend on something we have deliberately not defined in detail yet:

**what does a secure and compliant Wire Finance endpoint actually look like?**

We have already referenced concepts such as:

- BitLocker
- Microsoft Defender
- firewall controls
- security baselines
- compliance
- device risk
- Conditional Access

The next step is to bring those concepts together into a coherent security model.

**What security conditions should a Wire Finance device satisfy before it can be considered compliant — and eventually trusted as part of an access decision?**