---
layout: lesson
title: "Microsoft Defender Antivirus — Corporate Protection Policy"
series: wire-finance
parent: endpoint-security-and-compliance-implementation
order: 2
description: "Configure and verify the Wire Finance Microsoft Defender Antivirus baseline in Intune, including cloud protection, daily scans, managed exclusions, a harmless EICAR test and quarantine evidence."
---

BitLocker secured the data stored on our first Windows endpoint. Now we need to protect the **running device** against malicious files, scripts, suspicious downloads and potentially unwanted applications.

Windows 11 already includes Microsoft Defender Antivirus. In this lesson, we'll **manage its security settings centrally through Microsoft Intune** and prove that the policy works—rather than simply assuming a green check-in means the endpoint is protected.

Our approach is deliberately pilot-first: apply the enterprise antivirus candidate to Olivia Carter's device, verify the effective settings, test a scan and threat remediation, and record the evidence before expanding scope.

### Confirm the Device and Pilot Scope

| Item | Wire Finance configuration |
|---|---|
| VM asset | `WF-LT-001` |
| Actual Windows hostname | `DESKTOP-QDSCPUK` |
| Employee | Olivia Carter, Finance Analyst |
| Policy name | `WF-SEC-WIN-ANTIVIRUS` |
| Platform and profile | **Windows → Microsoft Defender Antivirus** |
| Assigned device group | `SG-DEVICES-WINDOWS-PILOT` |
| Scope tag | `Default` |

The pilot group uses **assigned membership**, and Olivia's enrolled device was added explicitly during the BitLocker implementation. Do not target `SG-DEVICES-AUTOPILOT-CORPORATE` or all Windows devices for this initial test.

### Confirm Antivirus Is Healthy Before Changing Its Policy

On the enrolled Windows VM, open **Windows PowerShell as Administrator** and run:

```powershell
Get-MpComputerStatus |
    Select-Object AMRunningMode,
                  AMServiceEnabled,
                  AntivirusEnabled,
                  RealTimeProtectionEnabled,
                  BehaviorMonitorEnabled,
                  OnAccessProtectionEnabled,
                  AntivirusSignatureLastUpdated
```

Our before-policy check established:

| Property | Observed value |
|---|---|
| `AMRunningMode` | `Normal` |
| `AMServiceEnabled` | `True` |
| `AntivirusEnabled` | `True` |
| `RealTimeProtectionEnabled` | `True` |
| `BehaviorMonitorEnabled` | `True` |
| `OnAccessProtectionEnabled` | `True` |
| `AntivirusSignatureLastUpdated` | 25 October 2026, 06:56 (displayed device time) |

![Defender Antivirus healthy on Olivia's VM before policy deployment]({{ '/assets/images/endpoint-security/wf-defender-before-policy-health.png' | relative_url }})

*Defender Antivirus was already active. Central policy enforcement is the work we are introducing—not the installation of a new antivirus product.*

### Create the Microsoft Defender Antivirus Policy

Go to the [Microsoft Intune admin center](https://intune.microsoft.com/):

**Endpoint security → Antivirus → Create Policy**

Choose **Platform: Windows** and **Profile: Microsoft Defender Antivirus**, then provide:

- **Name:** `WF-SEC-WIN-ANTIVIRUS`
- **Description:** `Wire Finance corporate Microsoft Defender Antivirus protection — pilot deployment`

Select **Next** to enter **Configuration settings**.

#### Core antivirus protection

Configure the following values. The UI uses **Allowed** for many features rather than **Enabled**.

| Setting | Wire Finance value |
|---|---|
| Allow Behavior Monitoring | **Allowed** |
| Allow Realtime Monitoring | **Allowed** |
| Allow On Access Protection | **Allowed** |
| Allow Cloud Protection | **Allowed** |
| Allow Archive Scanning | **Allowed** |
| Allow Email Scanning | **Allowed** |
| Allow Full Scan Removable Drive Scanning | **Allowed** |
| Allow scanning of all downloaded files and attachments | **Allowed** |
| Allow Scanning Network Files | **Allowed** |
| Allow Script Scanning | **Allowed** |
| Allow User UI Access | **Allowed** |
| Real Time Scan Direction | **Monitor all files (bi-directional)** |
| PUA Protection | **PUA Protection on — Block** |

This establishes continuous, behaviour-aware protection across files and downloads. Email scanning covers supported formats; it is **not** a substitute for Microsoft 365 email security.

#### Cloud-delivered protection and sample handling

| Setting | Wire Finance value |
|---|---|
| Cloud Block Level | **High** |
| Cloud Extended Timeout | **50** (up to 50 additional seconds) |
| Submit Samples Consent | **Send safe samples automatically** |

**High** is our pilot candidate for stronger cloud-based blocking; it may increase false positives or delays for unfamiliar files. We chose *safe samples automatically* rather than sending every sample automatically because Wire Finance may handle sensitive business information. This remains a risk-managed configuration choice, not a guarantee that every submitted item contains no sensitive information.

#### Scanning schedule, updates and performance

| Setting | Wire Finance value |
|---|---|
| Scan Parameter | **Quick scan** |
| Schedule Scan Day | **Every day** |
| Schedule Scan Time | **720** — 12:00 noon |
| Check For Signatures Before Running Scan | **Enabled** |
| Signature Update Interval | **4** hours |
| Enable Low CPU Priority | **Enabled** |
| Days To Retain Cleaned Malware | **60** days |

The scheduled noon Quick Scan suits our VirtualBox pilot, which may be switched off overnight. The schedule for wider deployment should be tested against real employee hours, device availability and workload. Real-time protection remains active between scheduled scans.

Avoid creating a second schedule through **Schedule Quick Scan Time** when the daily **Scan Parameter / Schedule Scan Time** combination already defines the desired scan.

#### Remediation and central governance

| Setting | Wire Finance value |
|---|---|
| Remediation action for Low severity threats | **Quarantine** |
| Remediation action for Moderate severity threats | **Quarantine** |
| Remediation action for High severity threats | **Quarantine** |
| Remediation action for Severe threats | **Quarantine** |
| Disable Local Admin Merge | **Disable Local Admin Merge** |
| Enable Network Protection | **Enabled (Audit mode)** |
| Excluded Paths, Extensions and Processes | **No custom entries** |

Quarantine allows a threat to be isolated and investigated. An explicit quarantine policy doesn't mean every detection is automatically remediated without interaction; we will verify the actual behaviour during testing.

**Disable Local Admin Merge** is an exclusion-governance control. It prevents locally configured list items from being merged into the effective centrally managed lists. Legitimate exclusions require central review rather than blanket local exceptions.

Network Protection is intentionally **Audit**, not **Block**. The antivirus policy owns that setting for this pilot; when we reach Attack Surface Reduction, evaluate the audit evidence rather than assigning an overlapping Network Protection configuration in another policy.

![Initial configuration review showing core antivirus selections and quick scan]({{ '/assets/images/endpoint-security/wf-defender-configuration-review.png' | relative_url }})

*An early Review + create screenshot, showing the core protection and scan choices. The advanced final values are documented in the tables above and verified later on the device; the image is not a complete final settings export.*

**Leave the other options Not configured unless there is a documented requirement.** In particular, we did not create broad exclusions, internal security-intelligence file shares, deprecated intrusion-prevention settings, server-only traffic parsers or special Defender update channels. A more mature baseline isn't simply one with every switch turned on.

### Assign and Create the Policy

On **Scope tags**, use **Default**, consistent with the Intune RBAC model already implemented for Wire Finance.

Under **Assignments**:

| Field | Selection |
|---|---|
| Included groups | `SG-DEVICES-WINDOWS-PILOT` |
| Excluded groups | **None** |
| First intended device | `DESKTOP-QDSCPUK` |

Select **Review + create**, review the values carefully, and click **Create**.

A manual **Sync** from the Intune device page can help during a pilot, but it isn't a requirement for every future employee. Enrolled Windows devices check in automatically, and sync-monitoring panes can be closed without cancelling delivery.

### Verify Intune Policy Delivery

In Intune, open:

**Endpoint security → Antivirus → WF-SEC-WIN-ANTIVIRUS**

Open **Device and user check-in status → View report**. Find Olivia's device, and check errors and conflicts. Our verified report shows:

| Result | Observed |
|---|---|
| Device | `DESKTOP-QDSCPUK` |
| Successful check-ins | **1** |
| Error | **0** |
| Conflict | **0** |
| In progress | **0** |

![Successful antivirus policy check in for DESKTOP-QDSCPUK]({{ '/assets/images/endpoint-security/wf-defender-policy-checkin-success.png' | relative_url }})

*Intune reports a successful antivirus policy check-in for Olivia's device, with no reported errors or conflicts. The account address is obscured in the publication image.*

For **Device assignment status**, select **Generate report** if it appears blank. A pending result or the sync pane saying **No policy updates** is not enough to conclude that the policy failed.

### Check individual setting reports

Open **Per setting status** and inspect the configured settings, particularly **Disable Local Admin Merge**.

![Intune per-setting report showing Disable Local Admin Merge and other configured settings succeeded]({{ '/assets/images/endpoint-security/wf-defender-per-setting-validation.png' | relative_url }})

*Disable Local Admin Merge reports one successful device, zero errors and zero conflicts. The other visible settings also report successful processing.*

This verifies **Intune's setting-level processing result**. A blank property in `Get-MpPreference` is not necessarily a failed local-admin-merge policy; use the per-setting report and, where additional assurance is required, inspect the effective policy and exclusions separately. The report does not constitute a simulated attempt to bypass the centrally managed exclusion policy.

### Compare the Effective Settings on Windows

On Olivia's VM, run the following in elevated PowerShell:

```powershell
Get-MpPreference |
    Select-Object PUAProtection,
                  CloudBlockLevel,
                  CloudExtendedTimeout,
                  SubmitSamplesConsent,
                  SignatureUpdateInterval,
                  DisableLocalAdminMerge,
                  ScanParameters,
                  ScanScheduleDay,
                  ScanScheduleTime,
                  EnableNetworkProtection
```

After the policy took effect, the device reported:

| Preference | Observed | Meaning |
|---|---|---|
| `PUAProtection` | `1` | Block |
| `CloudBlockLevel` | `2` | High |
| `CloudExtendedTimeout` | `50` | 50-second extension |
| `SubmitSamplesConsent` | `1` | Send safe samples automatically |
| `SignatureUpdateInterval` | `4` | Four-hour checks |
| `ScanParameters` | `1` | Quick scan |
| `ScanScheduleDay` | `0` | Every day |
| `ScanScheduleTime` | `12:00:00` | Noon |
| `EnableNetworkProtection` | `2` | Audit |
| `DisableLocalAdminMerge` | Blank in this view | Use Intune per-setting report above; avoid calling the blank result a failure or a separately verified effective value |

![PowerShell confirming High cloud protection, PUA block, daily quick scan and Network Protection audit mode]({{ '/assets/images/endpoint-security/wf-defender-effective-settings.png' | relative_url }})

*The effective Defender preferences match the policy's core protection and schedule choices. The local-admin-merge property is blank in this particular PowerShell output.*

A useful troubleshooting lesson emerged during the rollout: while assignment was **Pending**, the same command still showed earlier defaults—including PUA in Audit mode, standard cloud blocking, a 2:00 AM scan and Network Protection Off. Once the policy applied, the configured values appeared. **Do not overwrite Intune-managed preferences locally simply to make the numbers match.**

### Perform a Quick Scan and Confirm Completion

Run:

```powershell
Start-MpScan -ScanType QuickScan
```

Allow the scan to finish. Then retrieve its recorded times:

```powershell
Get-MpComputerStatus |
    Select-Object QuickScanStartTime,
                  QuickScanEndTime,
                  QuickScanAge
```

Our observed result on 26 October 2026 was:

| Measure | Actual result |
|---|---|
| Start | **08:41:32** |
| End | **08:43:26** |
| Duration | **1 minute 54 seconds** |
| `QuickScanAge` | `0` — recent scan |

![PowerShell verifying completed Defender quick scan]({{ '/assets/images/endpoint-security/wf-defender-quick-scan-completed.png' | relative_url }})

*The populated start and end times demonstrate that the Quick Scan completed. The scan report by itself does not indicate that malware was detected.*

### Test Detection Safely with EICAR

To test a detection without introducing real malware, follow the Windows steps in [Microsoft's antivirus detection test](https://learn.microsoft.com/en-us/defender-endpoint/validate-antimalware), which uses the standard harmless **EICAR** signature.

1. Create the approved EICAR test file in the Windows lab as instructed by Microsoft.
2. Do **not** disable real-time protection or add a Defender exclusion to make the test easier.
3. Open **Windows Security → Virus & threat protection** and inspect the initial detection.
4. If Defender shows **Start actions**, select it and choose **Quarantine** as appropriate.
5. Open **Protection history** and verify the final status.

The first screen showed **Virus:DOS/EICAR_Test_File**, **Severe**, **Active**, with an action required.

![Windows Security showing the initial EICAR detection as active]({{ '/assets/images/endpoint-security/wf-defender-eicar-detected.png' | relative_url }})

*EICAR was detected, but this screen does not yet prove remediation. The test file is harmless, even though Defender assigns it a virus detection name.*

After **Start actions** was used, Protection history showed **Threat quarantined**, with a 9:23 AM remediation record following the 9:22 AM detection.

![Windows Security Protection history confirming EICAR was quarantined]({{ '/assets/images/endpoint-security/wf-defender-eicar-quarantined.png' | relative_url }})

*The EICAR test item was quarantined. The user-profile path is obscured in the publication image.*

**Interpret this accurately:** we verified detection and **successful user-initiated remediation**. Because the initial screen required **Start actions**, this test is **not proof that quarantine always occurs automatically without interaction**. It is also not a real-malware effectiveness benchmark or evidence that Defender for Endpoint onboarding is complete.

### Common Issues and Solutions

| Symptom | What to check |
|---|---|
| Antivirus assignment displays **Pending** | Confirm `SG-DEVICES-WINDOWS-PILOT` membership, device connectivity and latest check-in; allow policy processing and reporting to update |
| Device assignment report is empty | Use **Generate report**, clear filters and refresh rather than rebuilding the policy |
| Sync monitor says **Policies: No policy updates** | Check the named antivirus policy's report; the sync pane is not the authoritative final result |
| PowerShell shows the previous default settings | Compare policy check-in and per-setting status; rerun `Get-MpPreference` after reporting finishes |
| `DisableLocalAdminMerge` is blank in `Get-MpPreference` | Inspect the policy's **Per setting status**; escalate to effective-policy inspection only if needed |
| EICAR remains **Active** | Complete the displayed Windows Security action and verify **Quarantined** in Protection history; don't disable Defender |
| High cloud blocking disrupts an approved application | Investigate the detection and application behaviour; evaluate centrally approved exceptions rather than broad exclusions |

![Intune sync-monitoring pane during early antivirus policy processing]({{ '/assets/images/endpoint-security/wf-defender-intune-sync-monitor.png' | relative_url }})

*The pilot sync monitor initially showed no policy update while application processing continued. Closing this pane only ends the monitoring view; it does not cancel ongoing device policy management.*

### Security Validation Checkpoint

| Evidence | Verified result |
|---|---|
| `WF-SEC-WIN-ANTIVIRUS` targeting | Assigned to `SG-DEVICES-WINDOWS-PILOT` |
| Intune device policy report | **1 Succeeded, 0 Error, 0 Conflict** |
| Local administrator merge policy | **1 Succeeded, 0 Error, 0 Conflict** in per-setting report |
| Antivirus operational state before policy | **Normal**, key protection flags `True` |
| Endpoint effective preferences after policy | **Matches the documented cloud, PUA, schedule, sample and Network Protection values** |
| Quick Scan | **Completed**, 26 October 2026, 08:41–08:43 |
| Safe test detection | **EICAR detected**, Severe |
| Remediation | **Quarantined after Start actions**, verified in Protection history |
| Normal Microsoft 365 app compatibility under High cloud blocking | **Continue routine pilot observation**; no separate post-policy test evidence captured in this lesson |
| Full Microsoft Defender for Endpoint onboarding | **Not yet** — later lesson |

**Outcome:** The Wire Finance antivirus policy is **implemented and functionally verified on Olivia's pilot device**. The policy is **not yet authorised for company-wide assignment**; application-compatibility monitoring and the remaining endpoint security controls continue before any broader rollout.

**Next: Chapter 9, Lesson 3 — Windows Defender Firewall.**

---

**Microsoft references:** [Intune Antivirus policy management](https://learn.microsoft.com/en-us/intune/device-configuration/endpoint-security/antivirus) · [Local administrator merge settings](https://learn.microsoft.com/en-us/defender-endpoint/configure-local-policy-overrides-microsoft-defender-antivirus) · [Defender CSP reference](https://learn.microsoft.com/en-us/windows/client-management/mdm/defender-csp) · [Antivirus detection test using EICAR](https://learn.microsoft.com/en-us/defender-endpoint/validate-antimalware)
