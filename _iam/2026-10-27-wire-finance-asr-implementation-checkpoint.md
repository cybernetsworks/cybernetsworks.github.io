---
layout: lesson
title: "Attack Surface Reduction — Company-Wide Rules and Pilot Validation"
series: wire-finance
parent: endpoint-security-and-compliance-implementation
order: 4
description: "Deploy and verify the Wire Finance Attack Surface Reduction standard in Intune, with three enforced rules, 15 audit rules, Controlled Folder Access evaluation and a safe WMI audit test."
---

BitLocker protects data at rest. Defender Antivirus detects malicious software. Windows Defender Firewall controls network traffic. **Attack Surface Reduction (ASR) goes a step further: it restricts behaviours attackers commonly exploit, even when the application involved is legitimate.**

For Wire Finance, the aim is a **company-wide Windows security standard**, not a Finance-only policy. We'll use one enrolled Windows 11 pilot device to validate the configuration before assigning it to other employees. Department-specific exceptions, if genuinely needed, will be designed separately and tested so they do not conflict with the core policy.

> **Our deployment approach:** enforce a small set of standard-protection rules immediately, place the remaining applicable rules in **Audit** mode, and collect evidence before increasing enforcement. **Audit does not block the activity it records.**

### 1. Confirm the Environment and Capture the Starting State

| Item | Wire Finance configuration |
|---|---|
| Policy | `WF-SEC-WIN-ASR` — intended final name |
| Platform and profile | **Windows → Attack Surface Reduction Rules** |
| Scope tag | `Default` |
| Included group | `SG-DEVICES-WINDOWS-PILOT` — assigned device group |
| Initial test endpoint | `DESKTOP-QDSCPUK` (VirtualBox asset `WF-LT-001`) |
| Pilot user | Olivia Carter |
| Later rollout | Additional company-managed Windows endpoints, after validation |

The endpoint was already Microsoft Entra joined and managed by Intune, with Microsoft Defender Antivirus operating in normal active mode and real-time protection enabled. BitLocker, Antivirus and Windows Firewall were implemented in the preceding lessons.

Before creating the ASR policy, open **Windows PowerShell as Administrator** on the pilot device:

```powershell
$p = Get-MpPreference

$ids = @($p.AttackSurfaceReductionRules_Ids | Where-Object { $_ })
$actions = @($p.AttackSurfaceReductionRules_Actions | Where-Object { $null -ne $_ })

if ($ids.Count -eq 0) {
    'No explicitly configured ASR rules found.'
} else {
    for ($i = 0; $i -lt [Math]::Min($ids.Count, $actions.Count); $i++) {
        [PSCustomObject]@{
            RuleId = $ids[$i]
            Action = $actions[$i]
        }
    }
}
```

Our pilot returned **`No explicitly configured ASR rules found.`** This established that we were not inheriting a list of explicitly configured ASR rules before this implementation. It does not mean that Defender Antivirus or the other endpoint protections were disabled.

![Defender returned no explicitly configured ASR rules before deployment]({{ '/assets/images/endpoint-security/wf-asr-before-policy-no-rules.png' | relative_url }})

*Baseline evidence captured before the policy was created.*

### 2. Create the Corporate ASR Policy

In the [Microsoft Intune admin center](https://intune.microsoft.com/), open:

**Endpoint security → Attack surface reduction → Create Policy**

Choose **Platform: Windows** and **Profile: Attack Surface Reduction Rules**. Enter:

| Field | Value |
|---|---|
| Name | `WF-SEC-WIN-ASR` |
| Description | `Wire Finance corporate attack surface reduction controls — pilot evaluation` |

Select **Next** to reach **Configuration settings**.

#### Choose the rule modes

Intune offers individual action modes for each ASR rule. Use the following Wire Finance configuration; do not simply set everything to **Block**.

| # | Attack Surface Reduction rule | Mode |
|---|---|---|
| 1 | Block abuse of exploited vulnerable signed drivers (Device) | **Block** |
| 2 | Block Adobe Reader from creating child processes | Audit |
| 3 | Block all Office applications from creating child processes | Audit |
| 4 | Block credential stealing from the Windows local security authority subsystem | **Block** |
| 5 | Block executable content from email client and webmail | Audit |
| 6 | Block executable files from running unless they meet a prevalence, age, or trusted list criterion | Audit |
| 7 | Block execution of potentially obfuscated scripts | Audit |
| 8 | Block JavaScript or VBScript from launching downloaded executable content | Audit |
| 9 | Block Office applications from creating executable content | Audit |
| 10 | Block Office applications from injecting code into other processes | Audit |
| 11 | Block Office communication application from creating child processes | Audit |
| 12 | Block persistence through WMI event subscription | **Block** |
| 13 | Block process creations originating from PsExec and WMI commands | Audit |
| 14 | Block untrusted and unsigned processes that run from USB | Audit |
| 15 | Block Win32 API calls from Office macros | Audit |
| 16 | Use advanced protection against ransomware | Audit |
| 17 | Block Webshell creation for Servers | **Not configured** |
| 18 | Block use of copied or impersonated system tools | Audit |
| 19 | Block rebooting machine in Safe Mode | Audit |

That gives us **three enforced Block rules and 15 evaluated Audit rules**, with the server-only webshell rule left unconfigured on our Windows 11 endpoint policy.

**Why those three Block rules?** Microsoft's standard-protection category includes the vulnerable signed-driver rule, LSASS credential-theft protection and WMI event-subscription persistence prevention. They provide meaningful initial enforcement without immediately restricting Office automation and administrative process creation across every department. Be aware that the driver rule does not stop an already-present vulnerable driver from loading, and the WMI persistence rule needs reassessment if we introduce Microsoft Configuration Manager.

**Why audit Office, scripts and remote administration?** Some legitimate tools, macros, deployment processes and Outlook/Excel integrations can trigger ASR rules. We want a record of the potential effect before moving those controls into Block mode. Audit events are evidence for a decision, not a promise that the rules have already prevented an attack.

**What about USB?** The ASR rule for *untrusted and unsigned processes that run from USB* targets suspicious executable content. It **does not disable USB ports or prevent ordinary file copying**. Restricting removable-storage reads and writes requires a separate Device Control design.

![Rule-by-rule Intune configuration review showing Block and Audit selections]({{ '/assets/images/endpoint-security/wf-asr-intune-rule-configuration.png' | relative_url }})

*Figure: The policy review lists the chosen ASR modes, including the three standard-protection Block rules.*

#### Configure Controlled Folder Access

Controlled Folder Access (CFA) evaluates attempts by untrusted applications to change files in protected folders. For the first corporate evaluation, set:

| Setting | Wire Finance value |
|---|---|
| Enable Controlled Folder Access | **Audit Mode** |
| Controlled Folder Access Protected Folders | No custom entries |
| Controlled Folder Access Allowed Applications | No custom entries |
| Attack Surface Reduction Only Exclusions | No entries |

Windows includes a built-in set of protected folders. We do not need to add broad folder exclusions or application exceptions before observing the normal business workload.

In Audit mode, CFA records qualifying activity rather than preventing file changes. **We have not enabled CFA blocking across Wire Finance.**

#### Keep policy ownership clear

Network Protection is already set to **Audit** in `WF-SEC-WIN-ANTIVIRUS`. Do not configure the same setting a second time in this ASR policy. Likewise, don't add global ASR exclusions for `C:\Windows`, all of Office, or entire user profiles. Review actual compatibility issues first; consider narrowly scoped exceptions only when justified.

### 3. Assign the Pilot — Not the Whole Company Yet

Select **Next** and configure:

| Wizard section | Selection |
|---|---|
| Scope tags | `Default` |
| Assignments → Include | `SG-DEVICES-WINDOWS-PILOT` |
| Assignments → Exclude | None |

Review the rule modes and exclusions before selecting **Create**.

This is a **corporate policy being validated through a pilot group**, not a policy scoped permanently to one employee or department. Broader assignment should follow compatibility testing and a controlled rollout decision.

> **Policy-name note:** The initial Intune report screenshots display `WIN-SEC-WIN-ASR`. During the final validation checklist, the administrator confirmed the naming correction to the approved `WF-SEC-WIN-ASR` convention. The historical screenshots have not been modified to invent a different original policy name.

### 4. Verify ASR Modes on Windows

Allow the managed device to receive the policy through its normal Intune check-in. For pilot testing, a manual device sync may help expedite reporting, but regular corporate operation does **not** require administrators to sync every endpoint individually.

On the test device, run:

```powershell
$p = Get-MpPreference

for ($i = 0; $i -lt $p.AttackSurfaceReductionRules_Ids.Count; $i++) {
    [PSCustomObject]@{
        RuleId = $p.AttackSurfaceReductionRules_Ids[$i]
        Action = $p.AttackSurfaceReductionRules_Actions[$i]
    }
}

$p.EnableControlledFolderAccess
```

Interpret the ASR actions as follows:

| Action value | Meaning |
|---|---|
| `1` | Block — rule actively enforces |
| `2` | Audit — rule evaluates without blocking |
| `0` | Disabled |
| `5` | Not configured |
| `6` | Warn, where supported |

Our actual PowerShell result showed **18 rule GUIDs: three with action `1` and fifteen with action `2`**. The final CFA value was `2`, confirming Audit mode.

![ASR rule IDs showing three Block and fifteen Audit values, with CFA audit mode]({{ '/assets/images/endpoint-security/wf-asr-rule-modes-confirmed.png' | relative_url }})

*Windows-side validation of the effective rule distribution and Controlled Folder Access Audit mode.*

To supplement those results, check **Event Viewer → Applications and Services Logs → Microsoft → Windows → Windows Defender → Operational**. **Event ID 5007** records Defender configuration changes, including our CFA mode and an ASR rule assigned to Audit:

![Defender Event ID 5007 confirms ASR and CFA configuration changes]({{ '/assets/images/endpoint-security/wf-asr-event-5007-configuration-change.png' | relative_url }})

*Figure: Configuration-change evidence. Event 5007 is not a threat detection by itself.*

### 5. Test One ASR Audit Rule Safely

For a controlled demonstration, test the Audit-mode rule **Block process creations originating from PsExec and WMI commands**. This is particularly useful because it shows the difference between **Audit** and **Block** without using malware or changing the managed security configuration.

In elevated PowerShell, run:

```powershell
Invoke-CimMethod -ClassName Win32_Process `
    -MethodName Create `
    -Arguments @{ CommandLine = 'cmd.exe /c echo WireFinanceASRTest' }
```

The VM returned **`ReturnValue : 0`** and **`ProcessId : 1700`**: Windows accepted the WMI process-creation request.

![Harmless WMI process creation test succeeded]({{ '/assets/images/endpoint-security/wf-asr-wmi-test-command.png' | relative_url }})

*The harmless WMI test was allowed to run because the relevant ASR rule was in Audit mode.*

Next query the Defender Operational event log:

```powershell
Get-WinEvent -FilterHashtable @{
    LogName = 'Microsoft-Windows-Windows Defender/Operational'
    Id = 1122
    StartTime = (Get-Date).AddMinutes(-10)
} -ErrorAction SilentlyContinue |
    Select-Object TimeCreated, Id, Message |
    Format-List
```

The test produced **Event ID 1122**, including the rule GUID:

```text
D1E49AAC-8F56-4280-B9BA-993A6D77406C
```

Defender recorded the WMI process-creation behaviour. Its message identified `WmiPrvSE.exe` and the test command. Two related 1122 records appeared in the captured event output; do not interpret them as two separate attacks.

![Defender audit Event ID 1122 for the WMI process creation test, with administrator identity redacted]({{ '/assets/images/endpoint-security/wf-asr-event-1122-audit-confirmed.png' | relative_url }})

*A real ASR audit event. The administrator account has been redacted from the public screenshot.*

**What this proves:** at least one configured ASR Audit rule recognised a relevant process-creation action, logged it and allowed it to proceed. It **does not** demonstrate that every ASR rule was triggered, that all three Block rules were functionally challenged, or that a real attack was stopped.

### 6. Confirm Intune Reporting and Application Compatibility

Back in Intune, open:

**Endpoint security → Attack surface reduction → WF-SEC-WIN-ASR**

Verify both the **Device and user check-in status** report and **Device assignment status**. If the assignment report appears empty, select **Generate report**.

During this implementation, the assignment report initially showed **Pending**. It later updated to **Success**, matching the separate one-device policy check-in report. Our final result was:

| Intune check | Actual result |
|---|---|
| Check-in | 1 Succeeded |
| Assignment | 1 Success |
| Errors | 0 |
| Conflicts | 0 |
| Pending | 0 |

![ASR policy check-in report showing one successful device]({{ '/assets/images/endpoint-security/wf-asr-intune-policy-checkin-success.png' | relative_url }})

![ASR device assignment report with one successful pilot device and identifying IDs redacted]({{ '/assets/images/endpoint-security/wf-asr-intune-device-assignment-success.png' | relative_url }})

*Intune confirmed successful processing for the pilot. The images retain the original policy title as captured before the naming correction.*

As a final practical check, confirm that **Word, Excel, Outlook and Teams** open normally and that normal document and spreadsheet editing works. The lab administrator reported these checks passed. They are an **initial application smoke test**, not proof that every finance application, macro, script or administrative workflow is compatible.

Review **Per setting status** when troubleshooting a specific rule, even if the overall policy succeeds.

### 7. Troubleshooting and Interpretation

| Observation | What to check |
|---|---|
| Policy remains Pending in Intune | Confirm the assigned device-group membership, check-in time, and the policy's own reporting; avoid recreating the policy simply because reporting is delayed. |
| No ASR events appear immediately | ASR events require matching activity. A clean event log alone neither validates nor invalidates a rule. |
| Event 5007 appears | This records a configuration change, not an intrusion or rule enforcement test. |
| Event 1122 appears | An ASR rule evaluated the activity in Audit mode; it did **not** block it. |
| Event 1121 appears | An ASR rule blocked matching activity. Investigate whether it was expected before making an exception. |
| CFA audit events appear | Review the affected application and protected folder before considering CFA Block mode. |
| An approved business tool is impacted by ASR | Check its triggering rule and process chain. Test a narrowly scoped exception where supported rather than excluding an entire application directory. |
| Policy naming differs from the intended standard | Correct **Properties → Basics** and record the change. Do not recreate a working policy merely to rename it. |

For concise evidence collection, use the following PowerShell query. It summarises actual rule activity rather than printing many long event messages:

```powershell
$events = @(Get-WinEvent -FilterHashtable @{
    LogName = 'Microsoft-Windows-Windows Defender/Operational'
    Id = 1121,1122,1123,1124
    StartTime = (Get-Date).AddDays(-2)
} -ErrorAction SilentlyContinue)

if ($events.Count -eq 0) {
    'No ASR or Controlled Folder Access detections recorded.'
} else {
    $events |
        Group-Object Id |
        Select-Object Name, Count |
        Format-Table -AutoSize
}
```

Our first query found no detections; the later controlled WMI activity generated 1122 events. That sequence is expected: **configuration alone does not generate an ASR detection.**

### 8. Implementation Outcome and Rollout Boundary

The Wire Finance pilot established the following results:

| Acceptance check | Evidence |
|---|---|
| Company-wide ASR candidate created and pilot-scoped | Intune policy and assigned device group |
| Three standard-protection rules in Block mode | Effective Windows ASR rule actions |
| Fifteen additional rules in Audit mode | Effective Windows ASR rule actions |
| Server webshell rule not configured | Policy configuration review |
| Controlled Folder Access in Audit mode | Windows preference and Event 5007 |
| One safe functional ASR test | WMI command success and Event 1122 |
| Intune policy and assignment | Both reported one successful device, no errors/conflicts |
| Basic Microsoft 365 application checks | Administrator-confirmed Word, Excel, Outlook and Teams smoke tests |

**This pilot is implemented, verified and documented for the tested controls.** The 15 Audit rules and CFA are **not enforced blocks**, and the WMI demonstration validates **one** audit rule. Before wider rollout, evaluate Office macros, business applications, administrative automation, ASR false positives and department-specific needs. Extend enforcement through controlled policy changes with compatible assignments—not overlapping policies that configure the same rule differently.

Microsoft Defender for Endpoint onboarding is covered in the next lesson; centralised Defender detection and hunting capabilities have **not** been claimed as part of this ASR deployment.

#### References

- [Microsoft Learn — Attack surface reduction rules overview](https://learn.microsoft.com/en-us/defender-endpoint/attack-surface-reduction)
- [Microsoft Learn — Configure ASR rules and exclusions](https://learn.microsoft.com/en-us/defender-endpoint/enable-attack-surface-reduction)
- [Microsoft Learn — ASR events in Windows Event Viewer](https://learn.microsoft.com/en-us/defender-endpoint/attack-surface-reduction-windows-events)
- [Microsoft Learn — Configure Controlled Folder Access](https://learn.microsoft.com/en-us/defender-endpoint/enable-controlled-folders)

**Next lesson: Microsoft Defender for Endpoint Onboarding.**