---
layout: lesson
title: "Device Security and Compliance Model"
series: wire-finance
parent: device-identity-provisioning
order: 9
description: "A managed device is not automatically a trusted device. See how Intune security configuration, Defender risk signals, compliance evaluation, and Conditional Access combine to determine whether a Wire Finance endpoint should be allowed to access corporate resources."
---

We now know how Wire Finance devices are:

- Owned
- Named
- Classified
- Joined
- Enrolled
- Provisioned
- Piloted
- Tested

But none of those stages independently answers the most important security question:

**Is this device secure enough to trust right now?**

A laptop may be company-owned.

It may be Microsoft Entra joined.

It may be enrolled into Intune.

It may even have been provisioned successfully through Windows Autopilot.

And it can still drift away from the security state Wire Finance expects.

That is why device security needs more than provisioning.

Wire Finance separates three ideas:

```text
Security Configuration
        ↓
What state should the device have?


Compliance Evaluation
        ↓
Does the device currently meet that state?


Access Enforcement
        ↓
What should the device be allowed to access?
```

Those responsibilities work together, but they are not interchangeable.

#### The Wire Finance Security and Compliance Model

Wire Finance uses:

- **Microsoft Intune** to configure and manage the desired endpoint security state
- **Microsoft Defender for Endpoint** to provide threat telemetry, detections, and device-risk signals
- **Microsoft Intune compliance** to evaluate whether a device currently meets the approved standard
- **Microsoft Entra Conditional Access** to eventually use those signals when deciding whether access should be allowed or restricted

The relationship looks like this:

![Wire Finance Device Security and Compliance Model]({{ '/assets/images/active-directory/device_security_compliance_model.png' | relative_url }})

The architecture can be understood as five connected decisions:

```text
1. Configure the Device
        ↓
2. Observe Threat and Risk
        ↓
3. Evaluate Compliance
        ↓
4. Enforce Access Policy
        ↓
5. Allow or Restrict Access
```

The governing principle is:

**security configuration establishes the desired state; compliance evaluates whether the device currently meets it; Conditional Access determines what access is permitted based on that state.**

---

#### Security Configuration Is Not Compliance

This distinction is easy to blur.

Suppose Wire Finance wants BitLocker enabled.

If we create a policy that says:

```text
Enable BitLocker
```

that is **security configuration**.

We are telling the device what state we want.

If we later evaluate:

```text
Is BitLocker Present?

YES
 ↓
Requirement Satisfied

NO
 ↓
Requirement Failed
```

that is **compliance**.

One tries to establish the required state.

The other checks whether the required state actually exists.

| Control | Purpose | Wire Finance Example |
|---|---|---|
| **Configuration policy** | Sets the intended device state | Configure Windows settings and management behaviour |
| **Endpoint security policy** | Configures a security technology | Defender Antivirus, BitLocker, Firewall, ASR, account protection |
| **Security baseline** | Applies a coordinated set of security settings | Windows / Defender baseline tuned for Wire Finance |
| **Compliance policy** | Evaluates whether the endpoint meets the required state | Check encryption, firewall, antivirus, OS, and device risk |
| **Conditional Access** | Uses identity and device signals to enforce access | Require a compliant device for selected resources |

So:

```text
CONFIGURATION
"Make the device secure."
```

is different from:

```text
COMPLIANCE
"Prove the device still meets the requirement."
```

and both are different from:

```text
CONDITIONAL ACCESS
"Decide what access is permitted."
```

---

#### Device Trust Is Built in Layers

Wire Finance treats endpoint trust as a progression rather than a single checkbox.

| Layer | Primary Platform | Question Answered |
|---|---|---|
| **1. Device identity** | Microsoft Entra ID | What device is this and which tenant identity does it belong to? |
| **2. Device management** | Microsoft Intune | Is the endpoint under approved management? |
| **3. Platform hardening** | Microsoft Intune | Has the desired secure configuration been applied? |
| **4. Threat protection** | Microsoft Defender for Endpoint | What threats, detections, and device-risk signals exist? |
| **5. Compliance evaluation** | Microsoft Intune | Does the device currently meet Wire Finance requirements? |
| **6. Access enforcement** | Microsoft Entra Conditional Access | Should access be allowed, restricted, or blocked? |

The progression becomes:

```text
Device Identity
      ↓
Device Management
      ↓
Platform Hardening
      ↓
Threat Protection
      ↓
Compliance Evaluation
      ↓
Access Enforcement
```

No single layer replaces the others.

#### Layer 1 — Device Identity

For normal Wire Finance corporate clients, the device is:

```text
Microsoft Entra Joined
```

This establishes the organisational device identity.

It answers:

**What device is this?**

But identity alone does not answer:

```text
Is it encrypted?

Is the firewall active?

Is Defender healthy?

Is the device compromised?

Is the operating system supported?

Is the device compliant?
```

So:

```text
Known Device
      ≠
Trusted Device
```

#### Layer 2 — Device Management

The corporate endpoint is also:

```text
Microsoft Intune Managed
```

Intune provides the management channel through which Wire Finance can configure and monitor the endpoint.

Together:

```text
Microsoft Entra Identity
        +
Microsoft Intune Management
        ↓
Managed Endpoint Foundation
```

That foundation is essential.

But management still does not mean that every required security condition is currently satisfied.

#### Layer 3 — Platform Hardening

The next layer establishes the desired secure device state.

Wire Finance plans to configure controls such as:

- BitLocker drive encryption
- Secure Boot
- Microsoft Defender Antivirus
- Windows Firewall
- Windows Hello for Business
- Attack Surface Reduction controls
- Operating-system security settings
- Endpoint security baselines
- Account-protection settings
- Windows LAPS
- Windows Update requirements

The exact policy values will be created and tested later during the Intune implementation phase.

At this stage, the important thing is defining the **control families** the endpoint architecture expects.

#### Modular Security Policies

Wire Finance will avoid building one enormous endpoint policy containing every security setting.

Instead, the implementation will remain modular.

Proposed security-policy objects include:

```text
WF-SEC-WIN-ANTIVIRUS
WF-SEC-WIN-FIREWALL
WF-SEC-WIN-BITLOCKER
WF-SEC-WIN-ASR
WF-SEC-WIN-ACCOUNT-PROTECTION
WF-SEC-WIN-EDR
WF-SEC-WIN-BASELINE
```

Their intended purposes are:

| Proposed Object | Purpose |
|---|---|
| `WF-SEC-WIN-ANTIVIRUS` | Microsoft Defender Antivirus configuration |
| `WF-SEC-WIN-FIREWALL` | Windows Firewall configuration |
| `WF-SEC-WIN-BITLOCKER` | Drive-encryption configuration |
| `WF-SEC-WIN-ASR` | Attack Surface Reduction configuration |
| `WF-SEC-WIN-ACCOUNT-PROTECTION` | Account protection and Windows LAPS-related controls |
| `WF-SEC-WIN-EDR` | Endpoint detection and response / Defender onboarding |
| `WF-SEC-WIN-BASELINE` | Wire Finance Windows security-baseline configuration |

Keeping the design modular gives us something very useful later:

```text
Problem Detected
      ↓
Identify Control Family
      ↓
Identify Relevant Policy
      ↓
Troubleshoot Smaller Scope
```

That is much easier than debugging one giant security policy containing hundreds of unrelated settings.

---

#### Layer 4 — Microsoft Defender for Endpoint

Corporate endpoints will eventually be onboarded to:

```text
Microsoft Defender for Endpoint
```

through the endpoint-management design.

Defender gives Wire Finance security information that goes beyond configuration.

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
Intune
      ↓
Configure and Manage Device State
```

while:

```text
Defender for Endpoint
      ↓
Observe Security Activity and Risk
```

The two systems can then contribute different signals to the security decision.

#### Defender Device Risk

A device can begin in a healthy state and later become risky.

For example:

```text
Device Initially Healthy
        ↓
Suspicious Activity Detected
        ↓
Defender Risk Increases
```

That should matter even if:

```text
Ownership = Corporate

Entra Join = Correct

Intune Enrollment = Correct
```

The device's original provisioning has not changed.

Its **security condition has**.

The intended signal chain is:

```text
Defender Detection / Risk Increase
        ↓
Device Risk Signal
        ↓
Intune Compliance Re-Evaluates
        ↓
Approved Risk Threshold Exceeded?
        ↓
Device Becomes Noncompliant
        ↓
Conditional Access Can Restrict Access
```

This is where endpoint detection starts influencing access control.

The organisation does not have to assume:

```text
"It was secure when we built it,
so it must still be secure."
```

Trust can be re-evaluated as the device state changes.

---

#### Layer 5 — Compliance Evaluation

Now Wire Finance asks:

**Does this device currently meet our acceptable security standard?**

The proposed standard corporate compliance policy is:

```text
WF-COMP-WIN-CORPORATE
```

targeted primarily to:

```text
SG-DEVICES-WINDOWS-CORPORATE
```

The initial design evaluates categories such as:

| Compliance Area | Wire Finance Design Requirement |
|---|---|
| **Management state** | Corporate endpoint must remain under approved Intune management |
| **BitLocker** | Required |
| **Secure Boot** | Required where supported by the approved hardware profile |
| **TPM** | Required for supported corporate devices |
| **Firewall** | Required and healthy |
| **Antivirus / antimalware** | Required and healthy |
| **Code integrity** | Required where supported by the Windows compliance profile |
| **Operating system** | Supported version; minimum build defined during implementation |
| **Defender device risk** | Must remain within the approved risk threshold |
| **Device activity** | Endpoint must continue checking in and being evaluated |

This policy does not configure all of those technologies.

It evaluates whether the required state exists.

#### Compliance Is a Current-State Decision

A device can move between states.

```text
Device Evaluated
      │
      ├── Requirements Satisfied
      │        ↓
      │     COMPLIANT
      │
      └── Requirement Failed
               ↓
          NONCOMPLIANT
```

For the Wire Finance design, the important business interpretation is:

```text
COMPLIANT
      ↓
Currently Meets Approved Requirements
```

versus:

```text
NONCOMPLIANT
      ↓
Remediation or Access Restriction Required
```

Compliance therefore represents a **current evaluation**, not a permanent badge issued when the laptop was deployed.

---

#### Privileged Devices Need Their Own Compliance Standard

A Privileged Access Workstation operates at a higher trust tier than an everyday corporate laptop.

So Wire Finance will not rely solely on:

```text
WF-COMP-WIN-CORPORATE
```

for PAWs.

The proposed privileged-device compliance policy is:

```text
WF-COMP-WIN-PAW
```

targeted to:

```text
SG-DEVICES-PRIVILEGED
```

The reason is simple:

```text
Normal Corporate Security Requirement
              ≠
Privileged Device Security Requirement
```

A PAW may require tighter expectations around:

```text
BitLocker
Secure Boot
TPM
Defender Health
Firewall
Code Integrity
Operating-System State
Defender Device Risk
Dedicated PAW Hardening
```

The PAW policy can therefore enforce a stricter definition of acceptable device state.

#### Compliance Still Does Not Grant Privilege

Even a fully compliant PAW does not automatically authorize administration.

The privileged-access chain still needs multiple controls to align.

For example:

```text
Approved adm-* Identity
        +
Strong Authentication
        +
WF-PAW-001
        +
PAW Compliant
        +
PIM Activation
        +
Correct PAG-* Authorization
        ↓
Sensitive Administration
```

So:

```text
Compliant PAW
      ≠
Automatically Authorized Administrator
```

Compliance contributes a device-trust signal.

Authorization remains a separate decision.

---

#### What Happens When Compliance Fails?

Compliance failure should trigger a controlled response rather than an improvised one.

The Wire Finance model is:

| Stage | Expected Action |
|---|---|
| **1. Failure detected** | Intune identifies the failed compliance requirement |
| **2. Device marked noncompliant** | The current device state becomes available to access-control decisions |
| **3. Remediation** | IT or the user remediates the issue where appropriate and safe |
| **4. Re-evaluation** | Intune checks the endpoint again after the required state is restored |
| **5. Access outcome** | Compliant devices can regain normal access; persistent noncompliance can remain restricted when Conditional Access is enforced |

Conceptually:

```text
Compliance Failure
        ↓
Device Marked Noncompliant
        ↓
Remediation
        ↓
Re-Evaluation
        ↓
Requirement Satisfied?
       /             \
     Yes              No
      ↓                ↓
Compliant          Remains
Restored           Noncompliant
                       ↓
                 Access Restrictions
                 May Apply
```

Not every failure needs the same response.

A lower-risk configuration issue may justify a remediation period.

A high-risk security condition — especially on a privileged device — may require a much faster response.

The principle is:

**response should be proportionate to the security risk, not merely to the existence of a red compliance status.**

#### An Unexpected Failure Is Not a Lab Test

This also connects directly to the previous lesson.

If a production endpoint unexpectedly becomes noncompliant:

```text
Unexpected Failure
      ↓
Operational / Security Issue
      ↓
Investigate
      ↓
Remediate
```

It does **not** automatically become:

```text
SG-DEVICES-NONCOMPLIANT-TEST
```

That group exists for approved intentional testing.

Real failure and controlled failure remain separate concepts.

---

#### What About Devices With No Compliance Policy?

There is another condition we need to design for.

Imagine:

```text
Corporate Device Exists
        +
Entra Joined
        +
Intune Enrolled
```

but no applicable compliance policy evaluates it.

Should we assume:

```text
No Policy
   =
Trusted
```

For the eventual Wire Finance production model:

**no.**

The target state is:

```text
No Applicable Compliance Policy
        ↓
NOT COMPLIANT
```

because an unevaluated corporate endpoint should not quietly inherit trusted status.

But this setting should **not** be tightened globally before the compliance architecture has been tested.

Otherwise:

```text
Security Improvement
        ↓
Enabled Too Early
        ↓
Legitimate Devices Fail Evaluation
        ↓
Conditional Access Reacts
        ↓
Everybody Has A Very Interesting Morning
```

So the safer sequence is:

```text
Pilot Compliance Policies
        ↓
Validate Device State
        ↓
Validate Remediation
        ↓
Validate Conditional Access
        ↓
Confirm Legitimate Devices
Can Become Compliant
        ↓
Then Tighten Tenant Behaviour
```

---

#### Layer 6 — Conditional Access

Compliance becomes much more powerful when it influences access.

Later, Microsoft Entra Conditional Access can use a requirement such as:

```text
Require Device to Be Marked as Compliant
```

for selected corporate resources.

That creates the chain:

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

The possible outcome is no longer merely a report saying:

```text
Noncompliant
```

It can eventually become:

```text
Compliant
      ↓
Access Allowed
```

or:

```text
Noncompliant / Unacceptable Risk
      ↓
Access Restricted or Blocked
```

#### Compliance Must Work Before Conditional Access Enforces It

The deployment order matters enormously.

Wire Finance will use:

```text
1. Configure Security
        ↓
2. Configure Compliance
        ↓
3. Confirm Devices Can Become Compliant
        ↓
4. Monitor Results
        ↓
5. Conditional Access in Report-Only
        ↓
6. Validate Impact
        ↓
7. Enforce Approved Policy
```

Not:

```text
Conditional Access Block First
        ↓
Figure Out Compliance Later
```

That sequence would make the access-control layer depend on a device-state model that has not yet been proven.

Instead:

**build the signal before enforcing the signal.**

---

#### Pilot-First Security Rollout

The groups we created earlier now become part of the security deployment strategy.

For new security or compliance configuration:

```text
New Policy
      ↓
SG-DEVICES-WINDOWS-PILOT
      ↓
Validate Normal Operation
      ↓
Review Impact
      ↓
Approve
      ↓
SG-DEVICES-WINDOWS-CORPORATE
```

The relevant populations are:

| Population | Purpose |
|---|---|
| `SG-DEVICES-WINDOWS-PILOT` | Receives candidate security and compliance changes first |
| `SG-DEVICES-WINDOWS-CORPORATE` | Receives validated production configuration |
| `SG-DEVICES-NONCOMPLIANT-TEST` | Exercises approved failure scenarios |
| `SG-DEVICES-PRIVILEGED` | Receives dedicated PAW hardening and stricter compliance requirements |

This is why the device-group design came before the security policies.

The groups provide the rollout boundaries.

#### Failure Testing Completes the Picture

Pilot testing answers:

```text
Does the Policy Work
Under Normal Conditions?
```

The noncompliance test group lets us ask:

```text
What Happens When
The Requirement Fails?
```

For example:

```text
SG-DEVICES-NONCOMPLIANT-TEST
        ↓
Approved Compliance Requirement Fails
        ↓
Observe Intune
        ↓
Observe Conditional Access
        ↓
Observe Defender / Security Telemetry
        ↓
Remediate
        ↓
Restore Known-Good State
```

A security architecture is stronger when we validate not only the healthy path but also the failure and recovery paths.

---

#### Standard Corporate Security Chain

For a normal Wire Finance laptop:

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

This represents the normal corporate trust path.

#### Privileged Security Chain

For a PAW:

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
Privileged Conditional Access
        ↓
Privileged Access Outcome
```

The technologies may be similar.

The security requirements are not.

---

#### Who Owns Each Security Signal?

No single Microsoft platform owns the entire endpoint-security story.

| Security Information | Authoritative Platform |
|---|---|
| **Physical ownership and assignment** | Wire Finance Asset Register |
| **Device identity** | Microsoft Entra ID |
| **Device management state** | Microsoft Intune |
| **Security configuration** | Microsoft Intune |
| **Compliance state** | Microsoft Intune |
| **Threat detections and device risk** | Microsoft Defender for Endpoint |
| **Access decision** | Microsoft Entra Conditional Access |

This separation matters during troubleshooting and investigations.

For example:

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
"Should it get access?"
        ↓
Microsoft Entra Conditional Access
```

Different systems contribute different evidence.

The trust decision is created by combining those signals.

---

#### Wire Finance Device Security and Compliance Decision

The current design can now be recorded as:

```text
Standard Security Population:
SG-DEVICES-WINDOWS-CORPORATE

Standard Compliance Policy:
WF-COMP-WIN-CORPORATE


Privileged Security Population:
SG-DEVICES-PRIVILEGED

Privileged Compliance Policy:
WF-COMP-WIN-PAW


Security Management:
Microsoft Intune

Threat Detection / Device Risk:
Microsoft Defender for Endpoint

Access Enforcement:
Microsoft Entra Conditional Access


Core Compliance Areas:
BitLocker
Secure Boot
TPM
Firewall
Antivirus / Antimalware
Code Integrity
Supported Windows Version
Defender Device Risk
Device Management / Activity


Security Policy Objects:
WF-SEC-WIN-ANTIVIRUS
WF-SEC-WIN-FIREWALL
WF-SEC-WIN-BITLOCKER
WF-SEC-WIN-ASR
WF-SEC-WIN-ACCOUNT-PROTECTION
WF-SEC-WIN-EDR
WF-SEC-WIN-BASELINE


Rollout Model:
Pilot
  ↓
Validate
  ↓
Production


Failure Testing:
SG-DEVICES-NONCOMPLIANT-TEST
```

The core principle remains:

**security configuration establishes the desired state. Compliance evaluates whether the endpoint currently meets it. Conditional Access uses the approved state when deciding what access should be permitted.**

#### Security Is Not the End of the Device Story

We now have a model for deciding whether a device is:

```text
Known
Managed
Configured
Protected
Compliant
Allowed to Access Resources
```

But devices do not stay in the same state forever.

They are issued.

Reassigned.

Repaired.

Lost.

Replaced.

Retired.

Sometimes wiped.

Sometimes recovered.

And sometimes removed from the environment entirely.

Security therefore cannot stop at:

```text
Device Is Compliant Today
```

We also need to know what happens to that device **throughout its life**.

**How should Wire Finance control a device from acquisition and assignment all the way through reassignment, recovery, retirement, and disposal?**