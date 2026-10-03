---
layout: lesson
title: "Pilot and Test Device Model"
series: wire-finance
parent: device-identity-provisioning
order: 8
description: "Pilot devices prove that a change works safely. Test devices deliberately exercise controlled failure paths. Wire Finance separates the two so new configurations can be validated without turning production endpoints into experiments."
---

We now know how Wire Finance provisions two very different types of endpoint.

A standard corporate laptop is built for everyday employee productivity.

A Privileged Access Workstation is built for sensitive administrative activity.

But before policies, applications, security controls, or compliance requirements reach the wider organisation, we need somewhere to prove that they behave as expected.

That introduces two more device roles:

**pilot devices** and **test devices**.

They may sound similar.

They are not.

A pilot device asks:

**Does this change work safely under normal operating conditions?**

A test device may ask:

**What happens when we deliberately make something fail?**

That distinction is the foundation of the Wire Finance testing model.

#### Pilot and Test Are Different Jobs

Wire Finance separates controlled rollout from controlled failure testing.

![Wire Finance Pilot vs Test Device Model]({{ '/assets/images/device-management/pilot_vs_test_device_model.png' | relative_url }})

The difference can be summarised simply:

```text
PILOT
   ↓
Introduce a Candidate Change
   ↓
Operate Normally
   ↓
Prove It Works Safely
   ↓
Decide Whether to Roll Out
```

while:

```text
TEST
   ↓
Start From a Known-Good State
   ↓
Introduce an Approved Failure Condition
   ↓
Observe Enforcement and Detection
   ↓
Remediate
   ↓
Return to Known-Good State
```

Both models support safer change.

But they answer different questions.

#### Pilot vs Noncompliance Test

| Area | Pilot Device | Noncompliance Test Device |
|---|---|---|
| **Primary group** | `SG-DEVICES-WINDOWS-PILOT` | `SG-DEVICES-NONCOMPLIANT-TEST` |
| **Membership** | Assigned | Assigned |
| **Purpose** | Validate a new configuration under normal working conditions before broad rollout | Deliberately validate failure, enforcement, detection, and remediation behaviour |
| **Typical device** | Selected managed corporate endpoint or dedicated pilot endpoint | Dedicated lab/test endpoint such as `WF-TST-001` |
| **Normal business use** | May be permitted where the change is sufficiently controlled | Not recommended during disruptive failure testing |
| **Autopilot classification** | Usually `CORPORATE` base build | Usually `CORPORATE` base build |
| **Expected outcome** | Change works safely and can be promoted | Failure occurs as expected, controls respond, and the device is restored |
| **Exit action** | Retain or remove from pilot group after rollout decision | Restore known-good state, verify compliance, and remove from test group |

There is a useful way to remember this:

```text
Pilot
=
"Can we safely deploy this?"
```

while:

```text
Test
=
"Does the control behave correctly when something goes wrong?"
```

---

#### Why Both Groups Use Assigned Membership

Both groups use:

```text
Membership Type:
Assigned
```

This is deliberate.

A device should not enter a pilot because some unrelated attribute happened to match a dynamic rule.

And a production endpoint should certainly not enter a disruptive testing population automatically because it became noncompliant.

Membership represents an **explicit operational decision**.

So:

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

This follows the principle we established earlier:

**where membership represents a deliberate decision, use deliberate assignment.**

#### The Pilot Device Model

Pilot devices are used for **staged rollout**.

They receive a candidate configuration before the wider corporate population so IT can observe what happens under normal conditions.

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

The difference is that it receives something **earlier**.

That might eventually include:

- An Intune configuration profile
- A new application
- A Windows setting
- An update configuration
- An endpoint-security policy
- A compliance-policy change
- A remediation
- Another managed endpoint change

The purpose is not to break the machine.

The purpose is to learn whether the proposed change behaves properly before exposing a larger population to it.

#### What Are We Validating During a Pilot?

A successful pilot means more than:

```text
The Policy Applied
```

Wire Finance should consider questions such as:

```text
Did the configuration apply correctly?

Does the device remain usable?

Did anything important stop working?

Are business applications still compatible?

Did security controls behave as expected?

Is Intune still reporting the device correctly?

Did the change create unexpected user impact?

Is the device still manageable?

Did monitoring reveal anything unexpected?
```

A technically successful deployment can still be a poor production change.

For example:

```text
Policy Applied Successfully
        +
Critical Business Application Breaks
        =
Pilot Failed
```

The purpose of the pilot is to discover that **before** the wider organisation discovers it for us.

#### Pilot Rollout Flow

A typical pilot follows this process:

| Stage | Pilot Action |
|---|---|
| **1. Change prepared** | IT creates or modifies the intended configuration and defines what success should look like |
| **2. Pilot scope selected** | Approved devices are added to `SG-DEVICES-WINDOWS-PILOT` |
| **3. Candidate configuration assigned** | The change targets the pilot group instead of the wider corporate population |
| **4. Normal operation validated** | IT reviews usability, compatibility, security impact, management health, and expected behaviour |
| **5. Results reviewed** | Problems are corrected or the change is approved |
| **6. Production rollout** | The validated change targets the intended production population, normally `SG-DEVICES-WINDOWS-CORPORATE` |
| **7. Pilot membership reviewed** | The device is retained as a standing pilot only where that remains useful |

Conceptually:

```text
Prepare
   ↓
Pilot
   ↓
Observe
   ↓
Fix if Necessary
   ↓
Approve
   ↓
Production
```

The pilot population acts as a controlled buffer between:

```text
New Idea
```

and:

```text
Production Fleet
```

#### A Pilot Device Can Still Be a Normal Corporate Device

Pilot membership does not replace the device's normal operational classification.

For example:

```text
WF-LT-001
│
├── SG-DEVICES-WINDOWS-CORPORATE
│      ↓
│   Operational corporate endpoint
│
└── SG-DEVICES-WINDOWS-PILOT
       ↓
    Receives selected changes first
```

There is no contradiction.

The first group answers:

```text
What is this device operationally?
```

The second answers:

```text
Should this device receive candidate changes before production?
```

That is another example of intentional group overlap.

---

#### The Noncompliance Test Device Model

Testing failure is a different activity.

Wire Finance uses:

`SG-DEVICES-NONCOMPLIANT-TEST`

for devices explicitly approved for controlled compliance, enforcement, and security-response testing.

The initial preferred endpoint is:

`WF-TST-001`

The goal is not to use ordinary employee devices as disposable experiments.

Instead, we begin from a known managed state and introduce a carefully controlled failure condition so we can observe what the environment does.

The principle is:

**a test device deliberately exercises the failure path.**

#### Why `WF-TST-001` Still Uses the `CORPORATE` Base Build

We do not currently need an Autopilot Group Tag called:

```text
TEST
```

`WF-TST-001` can begin with:

```text
Autopilot Classification:
CORPORATE
```

and receive the normal corporate foundation.

It can then be explicitly assigned to:

```text
SG-DEVICES-NONCOMPLIANT-TEST
```

when a test is required.

That gives us:

```text
CORPORATE Base Build
        ↓
Managed Corporate State
        ↓
Approved Test Assignment
        ↓
Controlled Test Scenario
```

This is useful because many tests are intended to answer:

**What happens when a normally managed corporate endpoint stops satisfying one of our requirements?**

Starting from a known corporate baseline gives us something meaningful to compare against.

#### Test Status Is an Operational Overlay

This distinction is important.

```text
TEST
```

does not currently represent a completely different provisioning architecture.

It represents **what we are temporarily doing with the device**.

So:

```text
Autopilot Classification
        ↓
CORPORATE
```

can coexist with:

```text
Operational Purpose
        ↓
Controlled Test
```

We use a device group for the temporary operational purpose rather than creating another provisioning class unnecessarily.

#### Controlled Test Flow

A noncompliance test follows a controlled lifecycle.

| Stage | Test Action |
|---|---|
| **1. Baseline recorded** | Confirm the endpoint begins in a known managed state and record expected behaviour |
| **2. Test approved** | IT/Security confirms the objective, scope, expected result, and impact |
| **3. Device selected** | A dedicated lab endpoint is added to `SG-DEVICES-NONCOMPLIANT-TEST` |
| **4. Failure condition introduced** | An approved test configuration causes a selected requirement to fail |
| **5. Enforcement observed** | Intune, Conditional Access, Defender/security telemetry, and relevant operational impact are reviewed |
| **6. Remediation validated** | The required configuration is restored and the recovery process is observed |
| **7. Known-good state confirmed** | Management, security, and compliance health are verified |
| **8. Test membership removed** | The device leaves the test group unless another approved scenario follows |

The lifecycle is therefore:

```text
Known-Good State
        ↓
Approved Test
        ↓
Controlled Failure
        ↓
Observe
        ↓
Remediate
        ↓
Validate Recovery
        ↓
Known-Good State
```

The beginning and end matter just as much as the failure in the middle.

#### Failure Must Be Intentional

There is a major difference between:

```text
Device unexpectedly becomes noncompliant
```

and:

```text
Device deliberately placed into
an approved noncompliance test
```

Those are not the same event.

An unexpected production failure is:

```text
Operational / Security Issue
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

That distinction prevents real incidents from being confused with lab activity.

---

#### What Can the Test Device Validate?

The exact policies will be built later.

At this stage, Wire Finance records the types of behaviour the lab should eventually be capable of validating.

| Scenario | What Wire Finance Validates |
|---|---|
| **Compliance requirement intentionally unmet** | Intune identifies the expected noncompliant state and reports the reason correctly |
| **Conditional Access compliant-device requirement** | Access behaviour reflects device compliance when the policy is later implemented |
| **Security configuration drift** | Management or security tooling detects or remediates an approved configuration deviation |
| **Recovery after remediation** | The endpoint returns to its expected compliant and managed state after the test condition is removed |

The important word is:

**reversible**.

The purpose of the lab is to learn how controls behave without unnecessarily damaging endpoints or exposing production users to disruptive experiments.

#### We Are Testing the Entire Control Chain

Suppose a future compliance test intentionally causes `WF-TST-001` to fail one approved requirement.

The interesting question is not merely:

```text
Did Intune say Noncompliant?
```

We may eventually want to observe the wider chain:

```text
Device State Changes
        ↓
Intune Detects State
        ↓
Compliance Evaluated
        ↓
Conditional Access Consumes Signal
        ↓
Access Behaviour Changes
        ↓
Security / Management Telemetry Generated
        ↓
Administrator Investigates
        ↓
Device Remediated
        ↓
Compliance Restored
```

That is much more valuable than proving a checkbox changes from green to red.

It allows the lab to validate whether multiple security controls work together as intended.

#### Pilot Success and Test Success Mean Different Things

This is another subtle but important distinction.

For a pilot, success normally means:

```text
Nothing Important Broke
        +
Desired Change Worked
```

For a noncompliance test, success may deliberately include:

```text
Requirement Failed
        +
Device Became Noncompliant
        +
Enforcement Occurred
        +
Monitoring Detected It
        +
Remediation Restored Trust
```

So a failure condition during the test can actually be part of a **successful test result**.

What matters is whether the environment responded as designed.

#### One Device Can Belong to Several Groups

`WF-TST-001` gives us a useful example of the device-group architecture working as intended.

It might belong to:

| Group | What That Membership Means |
|---|---|
| `SG-DEVICES-AUTOPILOT-CORPORATE` | The device receives the standard corporate Autopilot provisioning path |
| `SG-DEVICES-WINDOWS-CORPORATE` | It is an active company-owned Windows endpoint managed by Intune |
| `SG-DEVICES-WINDOWS-PILOT` | It receives selected candidate configurations before wider rollout |
| `SG-DEVICES-NONCOMPLIANT-TEST` | It is explicitly approved for controlled failure testing |

Those memberships describe different dimensions of the same device.

```text
Provisioning
        +
Operational State
        +
Pilot Role
        +
Testing Role
```

There is no requirement for the machine to belong to only one device group.

#### Why There Is No `PILOT` or `TEST` Autopilot Tag

We already decided that Autopilot classification should represent materially different **base provisioning paths**.

Current values are:

```text
CORPORATE
PAW
```

Pilot and test roles do not currently need separate base builds.

They are operational overlays.

So:

```text
PILOT
```

means:

```text
Receive Candidate Changes First
```

and:

```text
TEST
```

means:

```text
Approved for Controlled Test Activity
```

Neither means:

```text
Build a Fundamentally Different
Windows Endpoint From Scratch
```

Therefore no additional Group Tags are required at this stage.

This keeps the Autopilot design simple and prevents temporary activities from becoming permanent provisioning classes.

---

#### Production Devices Are Not Lab Equipment

Wire Finance needs a hard boundary around disruptive testing.

Production devices should not be casually added to:

`SG-DEVICES-NONCOMPLIANT-TEST`

If an existing production endpoint is going to become a dedicated lab device, that change should be deliberate.

Conceptually:

```text
Production Endpoint
        ↓
Lab Use Required
        ↓
Purpose Reviewed
        ↓
Device Deliberately Reclassified
        ↓
Approved Test Activity
```

The objective is to protect normal employee endpoints from experimental conditions that could interfere with business activity.

#### Privileged Workstations Stay Out of Ordinary Tests

`WF-PAW-###` devices have a different security purpose.

They should not be dropped into ordinary pilot or failure-testing exercises.

A PAW test would require its **own explicitly approved privileged-device scenario**.

That prevents an endpoint trusted for sensitive administration from quietly becoming general-purpose lab equipment.

The boundary is:

```text
PAW
      ↓
Privileged Administration

Test Endpoint
      ↓
Controlled Security Validation
```

A device should not casually drift between those purposes.

#### Every Test Needs an Exit Plan

Before introducing a controlled failure, Wire Finance should know:

```text
What are we testing?

What result do we expect?

Which device is involved?

What could be affected?

What evidence will we collect?

How will we restore the device?

How will we confirm recovery?

When does the test end?
```

A test without a recovery plan is not controlled testing.

It is just uncertainty with a laptop attached to it.

#### Membership Must Be Removed When the Purpose Ends

Because both groups use assigned membership, membership also needs lifecycle management.

For a pilot:

```text
Selected
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

Devices should not quietly remain in special-purpose groups forever.

Otherwise today's temporary experiment becomes next year's mystery policy assignment.

---

#### Wire Finance Pilot and Test Device Decision

The current model can now be recorded as:

```text
Pilot Group:
SG-DEVICES-WINDOWS-PILOT

Pilot Membership:
Assigned

Pilot Purpose:
Safe staged rollout and validation
before wider deployment


Test Group:
SG-DEVICES-NONCOMPLIANT-TEST

Test Membership:
Assigned

Primary Test Endpoint:
WF-TST-001

Base Provisioning:
CORPORATE Autopilot build unless
a future design requires otherwise

Test Purpose:
Controlled validation of compliance,
enforcement, security monitoring,
and remediation

Production Device Protection:
No automatic test membership.
Disruptive testing is restricted
to approved lab devices.

Lifecycle:
Explicit Add
      ↓
Validate / Test
      ↓
Restore / Review
      ↓
Remove
```

The governing principle is:

**pilot devices prove that a change works safely; noncompliance test devices deliberately exercise a failure path. Neither role replaces the device's corporate identity, ownership, management, or provisioning model.**

#### We Can Build and Test the Device — Now What Makes It Secure?

At this point, the endpoint architecture has become much clearer.

We know how to build:

```text
Standard Corporate Devices
Privileged Workstations
Pilot Devices
Test Devices
```

We also know how to separate:

```text
Provisioning Intent
Operational State
Pilot Participation
Privileged Trust
Controlled Testing
```

But several words have appeared repeatedly throughout the design:

```text
Defender
BitLocker
Firewall
Compliance
Device Risk
Security Baseline
Conditional Access
```

So far, we have deliberately treated many of those as future controls rather than pretending they already exist.

Now we need to define what those controls actually mean as a system.

**What security conditions should a Wire Finance device satisfy before it can be considered compliant — and eventually trusted during an access decision?**