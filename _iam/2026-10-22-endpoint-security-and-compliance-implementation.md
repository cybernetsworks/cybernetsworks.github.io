---
layout: lesson
title: "Endpoint Security and Compliance Implementation"
chapter: 9
series: wire-finance
author: Cybernetswork
image: assets/images/active-directory/endpoint_management_in_action.png
show_on_master: true
nav_id: endpoint-security-and-compliance-implementation
children_heading: "Explore the Security and Compliance Implementation"
description: "Secure the first Wire Finance Intune-managed Windows endpoint, verify each protection, and build a tested device-compliance standard before expanding deployment."
---

Our first Wire Finance Windows endpoint is up and running.

In Chapter 8, Olivia Carter signed in through Windows Autopilot, the device joined Microsoft Entra ID, and Intune took over management. Company Portal installed, and the required Microsoft 365 applications reached her desktop.

**But a managed device isn't automatically a secure device.**

Now we need to protect the data on that workstation, reduce its exposure to threats, and decide what evidence a device must provide before Wire Finance can consider it compliant.

### From Managed to Protected

We'll use **Microsoft Intune** to configure security controls, **Microsoft Defender for Endpoint** to provide endpoint threat and risk signals, and **Intune compliance policies** to evaluate whether the device meets our requirements.

```text
Autopilot + Microsoft Entra join + Intune enrollment
                         ↓
                Managed Windows device
                         ↓
    BitLocker · Antivirus · Firewall · ASR
    Endpoint detection · Account protection
                         ↓
          Verify the security settings
                         ↓
             Device compliance checks
                         ↓
       Compliant / Noncompliant + remediation
                         ↓
    Conditional Access (a later implementation)
```

Each layer answers a different question. **Security policies** define how the endpoint should be protected; **compliance policies** evaluate its reported state. Conditional Access will eventually use that evaluation when deciding whether access should be permitted, but we're **not enforcing those access decisions in this chapter**.

### One Device First, Then Wider Deployment

We'll begin with Olivia's Windows 11 Pro VirtualBox endpoint:

| Reference | Our pilot |
|---|---|
| Asset / VM name | `WF-LT-001` |
| Windows hostname | `DESKTOP-QDSCPUK` |
| Employee | Olivia Carter — Finance Analyst |
| Security-policy target | `SG-DEVICES-WINDOWS-PILOT` |
| Later corporate scope | `SG-DEVICES-WINDOWS-CORPORATE` |
| Controlled failure-test group | `SG-DEVICES-NONCOMPLIANT-TEST` |

Unlike our dynamic Autopilot classification group, **`SG-DEVICES-WINDOWS-PILOT` has assigned membership**. We added the enrolled device explicitly so that the first security policies don't automatically reach every corporate endpoint.

The scope is deliberate: **configure, verify, document, then expand** and establish baseline.

### Our Implementation Lessons

| Order | Lesson | What we'll establish |
|---|---|---|
| **1** | **BitLocker Encryption and Recovery-Key Protection** | Protect the OS drive with TPM-backed BitLocker, verify recovery-key escrow, and resolve the VirtualBox boot-media issue. |
| **2** | **Microsoft Defender Antivirus** | Configure and check real-time malware protection and the pilot's antivirus settings. |
| **3** | **Windows Defender Firewall** | Enforce the approved network-profile protections and verify them on Windows. |
| **4** | **Attack Surface Reduction** | Introduce ASR carefully, starting with suitable audit settings before evaluating enforcement. |
| **5** | **Microsoft Defender for Endpoint Onboarding** | Connect the endpoint to Defender and verify device visibility and threat telemetry. |
| **6** | **Windows Security Baseline and Hardening** | Review security-baseline settings, supported OS/update requirements, and conflicts with existing endpoint policies. |
| **7** | **Account Protection and Windows LAPS** | Configure supported local-account protections and test controlled local administrator password management. |
| **8** | **Windows Compliance Policy and Device Risk** | Define `WF-COMP-WIN-CORPORATE` and evaluate encryption, platform protection, and Defender risk where supported. |
| **9** | **Compliance Testing and Validation** | Test successful evaluation and deliberate failure/remediation without disrupting production users. |

We'll keep the controls modular. A single oversized baseline would make it harder to understand which setting caused a failure, especially while we're learning how Intune, Windows and Defender report different parts of the same device state.

### What We Have Proven So Far

The first completed security milestone is **`WF-SEC-WIN-BITLOCKER`**. On Olivia's VM, Intune reported a successful policy assignment, Windows showed **Protection On** with TPM and numerical recovery-password protectors, and the recovery-key identifier matched the record in Microsoft Entra ID.

That verification matters more than a green Intune policy status alone. During the test, Windows initially showed a fully encrypted drive with protection **Off** because a bootable Windows ISO remained attached to the VM. The BitLocker lesson explains the working configuration first and keeps this real troubleshooting case in its own section.

**BitLocker is complete; the other endpoint security and compliance controls are not yet being presented as implemented.** We'll add each result to the documentation only after testing it.

### What Success Will Mean

By the end of this chapter, we want evidence that our pilot device receives the approved hardening configuration, reports security and Defender signals where applicable, and can be evaluated reliably by an Intune compliance policy—including a controlled noncompliance scenario.

We won't treat a device as production-ready simply because Windows says *Compliant*. We'll verify the actual requirements behind that status and document what remains outside this chapter.

**Next: BitLocker Encryption and Recovery-Key Protection.**