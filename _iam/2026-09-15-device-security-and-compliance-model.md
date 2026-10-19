---
layout: lesson
title: "Device Security and Compliance Model"
series: wire-finance
parent: device-identity-provisioning
order: 9
description: "A managed device is not automatically a trusted device. See how Intune security configuration, Defender risk signals, compliance evaluation, and Conditional Access combine to determine whether a Wire Finance endpoint should be allowed to access corporate resources."
---

We now know how Wire Finance devices are:

- owned
- named
- classified
- joined
- enrolled
- provisioned
- piloted
- tested

But none of those stages independently answers one of the most important security questions:

**Is this device secure enough to trust right now?**

A laptop may be corporate-owned.

It may be Microsoft Entra joined.

It may be enrolled into Microsoft Intune.

It may even have completed Windows Autopilot successfully.

And it can still drift away from the security state Wire Finance expects.

A security control may be disabled.

The operating system may become outdated.

Malware may be detected.

The device may become risky.

Or the endpoint may simply stop checking in.

Provisioning therefore establishes the device.

Security and compliance determine whether that device continues to meet the organisation's expectations.

### Three Different Questions

Wire Finance separates three concepts that are easy to blur together.

```text
Security Configuration
        ↓
What state should the device have?

Compliance Evaluation
        ↓
Does the device currently meet
the approved requirements?

Access Enforcement
        ↓
Should that device state affect
access to company resources?
```

They are related.

They are not the same thing.

The core principle is:

**security configuration establishes the desired state, compliance evaluates the current state, and access controls can use that result when deciding whether access should be permitted.**

### The Wire Finance Security and Compliance Model

Wire Finance uses several Microsoft platforms for different parts of this process.

| Platform / Capability | Role in the Model |
|---|---|
| **Microsoft Intune** | Configures and manages the desired endpoint state |
| **Microsoft Defender for Endpoint** | Provides threat telemetry, detections, investigation capability, and device-risk signals |
| **Microsoft Intune Compliance** | Evaluates whether the endpoint currently meets defined security requirements |
| **Microsoft Entra Conditional Access** | Can later use device compliance and other signals when making access decisions |

![Wire Finance Device Security and Compliance Model]({{ '/assets/images/active-directory/device_security_compliance_model.png' | relative_url }})

Conceptually:

```text
Configure
    ↓
Observe
    ↓
Evaluate
    ↓
Make Signal Available
    ↓
Enforce Access Policy
```

No single product owns the entire trust decision.

Each one contributes a different part of the story.

### Security Configuration Is Not Compliance

Suppose Wire Finance wants BitLocker enabled.

A security policy that says:

```text
Enable BitLocker
```

is trying to establish the required state.

That is **configuration**.

A compliance policy that asks:

```text
Is BitLocker enabled?

YES
 ↓
Requirement Satisfied

NO
 ↓
Requirement Failed
```

is evaluating the state.

That is **compliance**.

And a later Conditional Access policy that says:

```text
Require a compliant device
```

uses the result when making an access decision.

That is **access enforcement**.

The distinction can be summarised as:

| Control | Purpose |
|---|---|
| **Configuration policy** | Establish the intended device configuration |
| **Endpoint security policy** | Configure a particular security technology |
| **Security baseline** | Apply a coordinated set of recommended security settings |
| **Compliance policy** | Evaluate whether required conditions currently exist |
| **Conditional Access** | Use approved identity and device signals when controlling access |

Or more simply:

```text
CONFIGURATION
"Make the device secure."

COMPLIANCE
"Check whether it still meets the requirement."

CONDITIONAL ACCESS
"Decide whether that state should affect access."
```

### Device Trust Is Built in Layers

Wire Finance does not treat trust as a single checkbox.

A corporate endpoint moves through several security layers.

| Layer | Primary Platform | Question |
|---|---|---|
| **Device identity** | Microsoft Entra ID | What organisational device is this? |
| **Device management** | Microsoft Intune | Is the device under approved management? |
| **Platform hardening** | Microsoft Intune | Has the expected security configuration been applied? |
| **Threat protection** | Microsoft Defender for Endpoint | What threats or device-risk signals currently exist? |
| **Compliance evaluation** | Microsoft Intune | Does the endpoint currently satisfy our requirements? |
| **Access enforcement** | Microsoft Entra Conditional Access | Should the device state influence access? |

Conceptually:

```text
Device Identity
      ↓
Management
      ↓
Security Configuration
      ↓
Threat Protection
      ↓
Compliance
      ↓
Access Enforcement
```

No layer replaces another.

For example:

```text
Known Device
      ≠
Secure Device
```

and:

```text
Managed Device
      ≠
Compliant Device
```

and:

```text
Compliant Device
      ≠
Automatically Authorised for Everything
```

Each signal answers a different question.

### Layer 1 — Device Identity

Normal Wire Finance corporate Windows endpoints are designed to become:

```text
Microsoft Entra Joined
```

This establishes an organisational identity for the device.

It helps answer:

**What device is this?**

But device identity alone does not tell us:

- whether BitLocker is enabled
- whether the firewall is healthy
- whether antivirus protection is active
- whether malware has been detected
- whether the operating system is supported
- whether the endpoint is compliant

The device may be known without being sufficiently trusted.

### Layer 2 — Device Management

Corporate endpoints are also designed to become:

```text
Microsoft Intune Managed
```

Intune gives Wire Finance the management channel needed to deploy settings, applications, security policies, compliance policies, and other endpoint controls.

Together:

```text
Microsoft Entra Device Identity
        +
Microsoft Intune Management
        ↓
Managed Corporate Endpoint Foundation
```

That foundation is important.

But management still does not prove that every required security condition is currently satisfied.

### Layer 3 — Platform Hardening

The next layer establishes the expected secure state of the Windows endpoint.

Wire Finance expects its corporate-device security model to cover areas such as:

- BitLocker drive encryption
- Secure Boot
- Trusted Platform Module
- Microsoft Defender Antivirus
- Windows Firewall
- Attack Surface Reduction
- Windows Hello for Business
- operating-system security settings
- account protection
- Windows LAPS
- endpoint detection and response
- Windows security baselines
- supported Windows versions and updates

The exact settings will be configured and tested during the Intune implementation phase.

At the design stage, the important thing is identifying the **control families** the endpoint architecture expects.

### Modular Security Policies

Wire Finance will avoid creating one enormous endpoint policy containing every security control.

Instead, the security configuration will remain modular.

Planned policy objects include:

| Planned Policy | Purpose |
|---|---|
| `WF-SEC-WIN-ANTIVIRUS` | Microsoft Defender Antivirus configuration |
| `WF-SEC-WIN-FIREWALL` | Windows Firewall configuration |
| `WF-SEC-WIN-BITLOCKER` | Drive-encryption configuration |
| `WF-SEC-WIN-ASR` | Attack Surface Reduction configuration |
| `WF-SEC-WIN-ACCOUNT-PROTECTION` | Account protection and local-account controls |
| `WF-SEC-WIN-EDR` | Microsoft Defender for Endpoint onboarding and EDR configuration |
| `WF-SEC-WIN-BASELINE` | Wire Finance Windows security-baseline configuration |

These names describe the planned implementation structure.

They do not mean the policies have already been deployed.

The modular approach gives Wire Finance a much easier troubleshooting model:

```text
Problem Detected
      ↓
Identify Control Family
      ↓
Identify Relevant Policy
      ↓
Investigate Smaller Scope
```

That is considerably easier than debugging one policy containing hundreds of unrelated settings.

### Layer 4 — Microsoft Defender for Endpoint

Configuration tells us what the device should look like.

Microsoft Defender for Endpoint tells us much more about what is **happening on the device**.

Corporate endpoints will eventually be onboarded to Defender for Endpoint.

Conceptually:

```text
WF-LT-001
      ↓
Microsoft Defender for Endpoint
      ↓
Telemetry
      +
Threat Detection
      +
Device Risk
      +
Investigation
      +
Response
```

This creates an important division of responsibility.

```text
Microsoft Intune
      ↓
Configure and Manage
the Endpoint
```

while:

```text
Microsoft Defender for Endpoint
      ↓
Observe Security Activity
and Device Risk
```

The two platforms contribute different evidence.

### Device Risk Can Change

A device may begin its life in a healthy state and later become risky.

For example:

```text
Device Healthy
      ↓
Suspicious Activity Detected
      ↓
Defender Device Risk Increases
```

That matters even if:

```text
Ownership = Corporate
Entra Join = Correct
Intune Enrollment = Correct
```

Nothing about the device's original provisioning has changed.

Its **security condition has**.

Where Defender for Endpoint and Intune are integrated, device risk can become part of the compliance evaluation.

Conceptually:

```text
Defender Detection
        ↓
Device Risk Signal
        ↓
Intune Compliance Evaluation
        ↓
Risk Above Approved Threshold?
        ↓
Device Can Become Noncompliant
```

This lets Wire Finance move away from the assumption:

```text
"It was secure when we deployed it,
so it must still be secure."
```

Device trust needs to respond when the device state changes.

### Layer 5 — Compliance Evaluation

Wire Finance now needs to answer:

**Does this device currently meet our approved security standard?**

The planned standard corporate Windows compliance policy is:

`WF-COMP-WIN-CORPORATE`

The intended production population is:

`SG-DEVICES-WINDOWS-CORPORATE`

The design expects the compliance model to evaluate areas such as:

| Compliance Area | Wire Finance Requirement |
|---|---|
| **BitLocker** | Required |
| **Secure Boot** | Required on supported corporate hardware |
| **TPM** | Required for approved corporate endpoints |
| **Code integrity** | Required where supported by the compliance profile |
| **Firewall** | Required |
| **Antivirus / antimalware** | Required and healthy |
| **Operating system** | Supported Windows version and approved minimum build |
| **Defender device risk** | At or below the approved risk threshold |
| **Device activity** | Device must continue reporting within the approved compliance-validity period |

The compliance policy does not configure all of those technologies.

It evaluates whether the required conditions are present.

### Compliance Represents Current State

Compliance should not be treated as a certificate permanently awarded when the device is deployed.

A device can move between states.

```text
Device Evaluated
      │
      ├── Requirements Satisfied
      │          ↓
      │      COMPLIANT
      │
      └── Requirement Failed
                 ↓
            NONCOMPLIANT
```

For Wire Finance:

```text
COMPLIANT
      ↓
Currently Meets
Approved Requirements
```

while:

```text
NONCOMPLIANT
      ↓
Something Requires
Investigation or Remediation
```

Compliance is therefore a **current evaluation**, not permanent trust.

### Privileged Devices Need a Higher Standard

A Privileged Access Workstation exists at a higher trust tier than a normal employee endpoint.

Wire Finance therefore intends to define a separate privileged-device compliance policy:

`WF-COMP-WIN-PAW`

targeted to:

`SG-DEVICES-PRIVILEGED`

The reason is simple:

```text
Standard Corporate Requirement
            ≠
Privileged Device Requirement
```

A PAW may require stricter expectations around:

- encryption
- Secure Boot
- TPM
- Defender health
- firewall state
- operating-system state
- code integrity
- device risk
- dedicated PAW hardening

The detailed values will be decided and tested during implementation.

### Compliance Does Not Grant Privilege

Even a perfectly compliant PAW does not make somebody an administrator.

For example:

```text
Compliant PAW
      ≠
Administrative Authorisation
```

The PAW contributes a **device-trust signal**.

Administrative authority still depends on the approved privileged identity and access model defined earlier in the journey.

In other words:

```text
Secure Device
      +
Approved Privileged Identity
      +
Approved Administrative Permission
      ↓
Controlled Privileged Activity
```

The device is only one part of that chain.

### What Happens When Compliance Fails?

Compliance failure should trigger a controlled response rather than an improvised one.

The basic model is:

| Stage | Expected Action |
|---|---|
| **Failure detected** | Intune identifies that a compliance requirement is not satisfied |
| **Device state changes** | Endpoint is evaluated as noncompliant according to the policy |
| **Investigation / remediation** | IT or Security determines why the requirement failed |
| **Re-evaluation** | Endpoint is evaluated again after the condition is corrected |
| **Access consequence** | Conditional Access may later restrict access while unacceptable noncompliance remains |

Conceptually:

```text
Compliance Failure
        ↓
Device Noncompliant
        ↓
Investigate
        ↓
Remediate
        ↓
Re-Evaluate
        ↓
Requirement Satisfied?
       /             \
     Yes              No
      ↓                ↓
 Compliant        Remains
 Restored         Noncompliant
```

Not every failure needs the same response.

A low-risk issue may justify a controlled remediation period.

A serious security condition may require much faster containment.

The principle is:

**the response should be proportionate to the security risk, not simply to the colour of a compliance indicator.**

### Real Failure Is Different From Test Failure

Wire Finance already has:

`SG-DEVICES-NONCOMPLIANT-TEST`

That group exists so the organisation can deliberately exercise failure scenarios.

An unexpected production failure is different.

```text
Production Device
Unexpectedly Fails Requirement
        ↓
Operational / Security Issue
        ↓
Investigate and Remediate
```

It should not automatically become a test device.

Likewise:

```text
SG-DEVICES-NONCOMPLIANT-TEST
        ↓
Approved Failure Scenario
        ↓
Observe Expected Behaviour
        ↓
Restore Known-Good State
```

Controlled testing and real security failure remain separate concepts.

### What About a Device With No Compliance Policy?

There is another condition Wire Finance needs to plan for.

Imagine a device that is:

```text
Corporate
      +
Microsoft Entra Joined
      +
Intune Enrolled
```

but no applicable compliance policy evaluates it.

Should the absence of an evaluation mean:

```text
No Policy
    =
Trusted
```

For the eventual Wire Finance production model:

**no.**

The target is for devices without an applicable compliance policy to be treated as:

```text
No Applicable Compliance Policy
        ↓
NOT COMPLIANT
```

That avoids allowing an unevaluated endpoint to inherit trusted status.

However, this tenant-wide behaviour should **not** be tightened before the compliance architecture is working correctly.

The safer sequence is:

```text
Create Pilot Compliance Policy
        ↓
Validate Legitimate Devices
Can Become Compliant
        ↓
Validate Failure and Remediation
        ↓
Validate Access-Control Impact
        ↓
Then Tighten
No-Policy Behaviour
```

The principle is:

**prove the compliance model before making the absence of compliance a blocking condition.**

### Layer 6 — Conditional Access

Compliance becomes much more valuable when the result can influence access.

Later in the project, Microsoft Entra Conditional Access can use a grant requirement such as:

```text
Require device to be marked as compliant
```

for selected resources and populations.

That creates the signal chain:

```text
Device Security State
        ↓
Compliance Evaluation
        ↓
Compliance Signal
        ↓
Conditional Access
        ↓
Access Decision
```

A noncompliant device can therefore become more than a dashboard warning.

Its state can eventually influence whether organisational resources are accessible.

The detailed Conditional Access policies belong in the Entra implementation stage.

This page only establishes the dependency:

**Conditional Access should consume a compliance signal that has already been tested and proven.**

### Build the Signal Before Enforcing the Signal

The deployment order matters.

Wire Finance will follow:

```text
1. Configure Security Controls
        ↓
2. Configure Compliance
        ↓
3. Confirm Legitimate Devices
   Can Become Compliant
        ↓
4. Test Failure and Remediation
        ↓
5. Monitor Results
        ↓
6. Introduce Conditional Access
   in Report-Only
        ↓
7. Validate Impact
        ↓
8. Enforce the Approved Policy
```

Not:

```text
Block Access First
        ↓
Figure Out Compliance Later
```

The principle is:

**build the signal before enforcing the signal.**

This also reduces the risk of locking legitimate employees out because the device-state model has not yet been proven.

### Pilot-First Security Rollout

Wire Finance will not deploy new endpoint-security controls directly to every production device.

The rollout follows the pilot model established earlier.

```text
New Security or Compliance Policy
        ↓
SG-DEVICES-WINDOWS-PILOT
        ↓
Validate Normal Operation
        ↓
Test Failure and Recovery
        ↓
Review Impact
        ↓
Approve
        ↓
SG-DEVICES-WINDOWS-CORPORATE
```

The main populations are:

| Population | Purpose |
|---|---|
| `SG-DEVICES-WINDOWS-PILOT` | Receives candidate security and compliance changes first |
| `SG-DEVICES-WINDOWS-CORPORATE` | Receives validated standard configuration |
| `SG-DEVICES-NONCOMPLIANT-TEST` | Exercises approved failure scenarios |
| `SG-DEVICES-PRIVILEGED` | Receives PAW-specific hardening and stricter compliance requirements |

This is why the device-group design came before the security controls.

The groups provide the deployment boundaries.

### Test the Failure Path Too

A successful pilot should not prove only that the healthy path works.

It should also answer:

```text
What happens when
a requirement fails?
```

For example:

```text
SG-DEVICES-NONCOMPLIANT-TEST
        ↓
Approved Requirement Fails
        ↓
Observe Intune Compliance
        ↓
Observe Security Telemetry
        ↓
Observe Conditional Access
when later enabled
        ↓
Remediate
        ↓
Restore Known-Good State
```

A security design is stronger when both the success path and the failure/recovery path are understood.

### Standard Corporate Security Chain

The intended trust path for a standard corporate endpoint is:

```text
SG-DEVICES-WINDOWS-CORPORATE
        ↓
Intune Security Controls
        ↓
Microsoft Defender for Endpoint
        ↓
WF-COMP-WIN-CORPORATE
        ↓
Compliance State
        ↓
Microsoft Entra Conditional Access
        ↓
Access Outcome
```

This represents the normal corporate-device security model.

### Privileged Device Security Chain

A privileged device follows a stricter path.

```text
SG-DEVICES-PRIVILEGED
        ↓
Dedicated PAW Hardening
        ↓
Microsoft Defender for Endpoint
        ↓
WF-COMP-WIN-PAW
        ↓
Stricter Compliance State
        ↓
Privileged Access Controls
        ↓
Administrative Access Outcome
```

The Microsoft technologies may be similar.

The required trust level is not.

### Who Owns Each Security Signal?

No single platform owns the entire endpoint-security story.

| Security Information | Authoritative Source |
|---|---|
| **Physical ownership and assignment** | Wire Finance Asset Register |
| **Device identity** | Microsoft Entra ID |
| **Device management state** | Microsoft Intune |
| **Security configuration** | Microsoft Intune |
| **Compliance state** | Microsoft Intune |
| **Threat detections and device risk** | Microsoft Defender for Endpoint |
| **Access enforcement** | Microsoft Entra Conditional Access |

That distinction becomes useful during troubleshooting.

```text
"What device is this?"
        ↓
Microsoft Entra ID
```

```text
"Is it managed?"
        ↓
Microsoft Intune
```

```text
"Is it compliant?"
        ↓
Microsoft Intune
```

```text
"Is it risky?"
        ↓
Microsoft Defender for Endpoint
```

```text
"Should this state affect access?"
        ↓
Microsoft Entra Conditional Access
```

Different systems provide different evidence.

The trust decision is created by combining those signals.

### Wire Finance Security and Compliance Design

The device-security design can now be summarised as:

```text
Standard Device Population:
SG-DEVICES-WINDOWS-CORPORATE

Standard Compliance Policy:
WF-COMP-WIN-CORPORATE


Privileged Device Population:
SG-DEVICES-PRIVILEGED

Privileged Compliance Policy:
WF-COMP-WIN-PAW


Security Configuration:
Microsoft Intune

Threat Detection / Device Risk:
Microsoft Defender for Endpoint

Access Enforcement:
Microsoft Entra Conditional Access


Planned Security Policy Families:
WF-SEC-WIN-ANTIVIRUS
WF-SEC-WIN-FIREWALL
WF-SEC-WIN-BITLOCKER
WF-SEC-WIN-ASR
WF-SEC-WIN-ACCOUNT-PROTECTION
WF-SEC-WIN-EDR
WF-SEC-WIN-BASELINE


Rollout:
Pilot
  ↓
Validate
  ↓
Production


Failure Testing:
SG-DEVICES-NONCOMPLIANT-TEST
```

The core principle remains:

**security configuration establishes the desired state. Compliance evaluates whether the device currently meets that state. Defender contributes threat and risk information. Conditional Access can then use the approved signals when determining access.**

### Security Is Not the End of the Device Story

Wire Finance now has a model for determining whether a device is:

```text
Known
  ↓
Managed
  ↓
Configured
  ↓
Protected
  ↓
Evaluated
  ↓
Compliant
  ↓
Able to Contribute
to Access Decisions
```

But devices do not remain in one state forever.

They are assigned.

Reassigned.

Repaired.

Lost.

Recovered.

Rebuilt.

Replaced.

Retired.

And eventually removed from service.

Security therefore cannot stop at:

```text
Device Is Compliant Today
```

We also need to understand what happens to the endpoint **throughout its life**.

The final part of the Device Provisioning Model therefore asks:

**How should Wire Finance control a device from acquisition and assignment through reassignment, recovery, retirement, and disposal?**

That takes us into the **Device Lifecycle**.