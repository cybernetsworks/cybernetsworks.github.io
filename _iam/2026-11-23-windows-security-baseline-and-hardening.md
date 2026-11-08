---
layout: lesson
title: "Windows Security Baseline and Hardening — Pilot Deployment and Validation"
series: wire-finance
parent: endpoint-security-and-compliance-implementation
order: 6
description: "Customise the Windows 26H2 Intune security baseline around our existing endpoint policies, deploy it remotely to the pilot, and verify its effect on Windows auditing and event logging."
---

Our Windows pilot is now onboarded to Microsoft Defender for Endpoint, and the security team can investigate its activity remotely.

But **being able to investigate a device** is different from **having a consistent security configuration enforced on it**.

In earlier lessons, we configured BitLocker, Defender Antivirus, Firewall, Attack Surface Reduction (ASR) and EDR onboarding. Now we need to harden Windows itself without allowing a new policy to undo those decisions.

Microsoft provides security baselines for this purpose. The challenge is that a baseline already contains configured defaults. **Which settings should we keep, which should remain under our existing policies, and which are unsuitable for the VirtualBox pilot?**

We'll answer those questions before deployment, then use Intune to deliver the configuration remotely and check what actually changes on Windows.

### Choose the Windows Security Baseline

I opened the [Microsoft Intune admin center](https://intune.microsoft.com/) and followed:

**Endpoint security → Security baselines**

Our tenant listed several templates, including baselines for Defender for Endpoint, Microsoft Edge, Microsoft 365 Apps and **Security Baseline for Windows 10 and later**.

![Available security baseline templates in Intune]({{ '/assets/images/endpoint-security/wf-baseline-templates-26h2.png' | relative_url }})

*The Windows security baseline was available in version 26H2.*

I selected **Security Baseline for Windows 10 and later — version 26H2**. Despite the template's name, it also covers Windows 11.

Here is the profile we planned for the pilot:

| Setting | Value used |
|---|---|
| **Profile name** | `WF-SEC-WIN-BASELINE` |
| **Platform** | Windows |
| **Baseline version** | 26H2 |
| **Scope tag** | `Default` |
| **Target group, once ready** | `SG-DEVICES-WINDOWS-PILOT` |
| **Pilot device** | `DESKTOP-QDSCPUK` (asset `WF-LT-001`) |

**Why not assign the template immediately?** Because its default settings are already selected. Leaving a setting untouched is not necessarily the same as leaving it unconfigured. We need to review what Intune would enforce before it reaches the device.

### Check the VM Before Enabling Hardware-Backed Protection

The pilot reported **Windows 11 Enterprise**, version `10.0.26300` (Windows 11 26H2).

I then opened PowerShell as Administrator on the VM to check whether the hardware-dependent protections proposed by the baseline were already operating:

```powershell
Get-CimInstance -Namespace root\Microsoft\Windows\DeviceGuard `
    -ClassName Win32_DeviceGuard |
    Select-Object VirtualizationBasedSecurityStatus,
                  SecurityServicesConfigured,
                  SecurityServicesRunning,
                  AvailableSecurityProperties |
    Format-List

Confirm-SecureBootUEFI
```

| Check | Observed result |
|---|---|
| **Secure Boot** | `True` |
| **VirtualizationBasedSecurityStatus** | `0` — VBS not enabled |
| **SecurityServicesConfigured** | `{0}` |
| **SecurityServicesRunning** | No services listed |
| **AvailableSecurityProperties** | `{2}` — Secure Boot reported |

![PowerShell Device Guard results before the baseline]({{ '/assets/images/endpoint-security/wf-baseline-vbs-predeployment.png' | relative_url }})

*Secure Boot was enabled, but VBS was not active on our VirtualBox device.*

That matters because **Device Guard** initially selected **Credential Guard with UEFI lock**, while **Virtualization Based Technology** selected **HVCI with UEFI lock**.

![Initial UEFI-lock defaults under Device Guard]({{ '/assets/images/endpoint-security/wf-baseline-device-guard-uefi-defaults.png' | relative_url }})

*The initial baseline would have attempted to configure firmware-locked protection.*

This test does **not** prove that VirtualBox can never support VBS. It tells us that the required configuration had not been demonstrated on this VM. Rather than risk a difficult-to-reverse change, we checked the available options.

![Credential Guard dropdown with Not configured available]({{ '/assets/images/endpoint-security/wf-baseline-credential-guard-not-configured-option.png' | relative_url }})

The **Not configured** option let us exclude Credential Guard from this baseline without selecting **Disabled**. The same principle guided our other VirtualBox-specific exclusions.

### Configure the Baseline Without Duplicating Existing Policies

I selected **Create profile** and entered the name shown above. On **Configuration settings**, I reviewed the categories from **Administrative Templates** to **Printers**.

One rule shaped the configuration: **a security control should have one clear policy owner wherever practicable**.

For example, our ASR policy already has **three rules in Block mode and 15 in Audit**, and our Antivirus policy uses **Network Protection in Audit mode**. Accepting different Defender defaults in the baseline could have conflicted with those decisions.

These are the main changes we made:

| Baseline area | Configuration used | Why |
|---|---|---|
| **Defender** | **Not configured** | Keep Antivirus and ASR under their existing policies |
| **Firewall** | **Not configured** | Preserve `WF-SEC-WIN-FIREWALL` ownership |
| **Device Guard** | **Not configured** | Defer VBS, Credential Guard and related settings until supported hardware is validated |
| **Virtualization Based Technology** | **Not configured** | Avoid untested HVCI and UEFI lock |
| **Data Protection / Dma Guard** | **Not configured** | Avoid untested DMA restrictions on the VM |
| **Local Security Authority** | **Not configured** | Defer the baseline's firmware-locked protection option |
| **User Rights** | **Not configured** | Avoid changing sign-in and recovery rights during this pilot |
| **Windows Hello for Business** | **Not configured** | Leave authentication changes outside this hardening policy |
| **LAPS** | **Not configured** | Reserve local administrator password protection for Lesson 7 |
| **Printers (top-level category)** | **Not configured** | Defer that category's printer-specific control |
| **Overlapping BitLocker / Defender Administrative Templates** | **Not configured** | Leave the earlier policies in charge |

The separate **Administrative Templates → Printers** settings remained configured, including print-driver and RPC restrictions. Similarly, the Local Security Authority exclusion did not remove all LSASS-related hardening from Administrative Templates.

We kept selected Microsoft hardening defaults that added protections not already owned by our earlier profiles:

| Control | Value used |
|---|---|
| **Process Creation auditing** | Success |
| **Credential Validation auditing** | Success and Failure |
| **Group Membership auditing** | Success |
| **File Share auditing** | Success and Failure |
| **Security Event Log maximum** | `196608` KB (**192 MiB**) |
| **Application and System Event Log maxima** | `32768` KB (**32 MiB**) each |
| **PowerShell Script Block Logging** | Enabled |
| **SMBv1** | Disabled through the relevant settings |
| **SMB signing** | Required |
| **Interactive inactivity limit** | 900 seconds |
| **Minimum device password length / history** | 14 characters / 24 passwords |

One default needed an intentional adjustment.

Under **Local Policies Security Options → User Account Control Behavior Of The Elevation Prompt For Standard Users**, I changed **Automatically deny elevation requests** to **Prompt for credentials on the secure desktop**.

**Why?** Olivia remains a standard user, but our pilot still needs a supported way for an authorised administrator to approve an elevation request. Requiring administrator credentials keeps that separation in place. A production deployment might choose the stricter option if its support model requires it.

We retained other hardening settings, including SmartScreen, SMB protocol restrictions, remote-management safeguards and auditing. Those choices were suitable for a **controlled pilot**, not yet proof that every application, printer or older file share across Wire Finance would work unchanged.

### Create the Profile Without Assigning It

Finishing configuration is not the same as deploying it.

To keep the two stages separate, I returned to the profile wizard and:

1. Checked the configured settings under **Review + create**.
2. Left the scope tag as **Default**.
3. Removed the pilot group from **Assignments**, leaving **no included groups**.
4. Selected **Create**.
5. Returned to **Security Baseline for Windows 10 and later → Profiles** to confirm the profile existed.

Intune showed `WF-SEC-WIN-BASELINE`, version **26H2**, with **Assigned: No**.

![Created profile with Assigned No]({{ '/assets/images/endpoint-security/wf-baseline-profile-created-unassigned.png' | relative_url }})

*The baseline was saved successfully but was not yet deployed.*

This gave us a clean checkpoint before touching the pilot endpoint.

### Record the Windows State Before Deployment

Before assigning the baseline, we needed evidence of what Windows was already doing. Otherwise, a successful deployment report would not tell us which protections had actually changed.

First, I checked the system drive:

```powershell
Get-BitLockerVolume -MountPoint C: |
    Select-Object MountPoint, VolumeStatus,
                  ProtectionStatus, EncryptionPercentage
```

The result was **FullyEncrypted**, **Protection On**, with **100% encryption**. Secure Boot also reported `True`.

![BitLocker and Secure Boot verified before the baseline]({{ '/assets/images/endpoint-security/wf-baseline-bitlocker-before-deployment.png' | relative_url }})

*The existing disk-protection configuration was still healthy.*

Next, I ran these read-only checks:

```powershell
auditpol /get /category:*

Get-SmbServerConfiguration |
    Select-Object EnableSMB1Protocol,
                  EnableSMB2Protocol,
                  RequireSecuritySignature

Get-SmbClientConfiguration |
    Select-Object EnableInsecureGuestLogons,
                  RequireSecuritySignature

Get-WinEvent -ListLog Security |
    Select-Object LogName, MaximumSizeInBytes
```

Four audit subcategories showed **No Auditing**: **Process Creation**, **Credential Validation**, **Group Membership** and **File Share**. These would become our post-deployment tests.

![Audit policy before the baseline]({{ '/assets/images/endpoint-security/wf-baseline-auditing-before-deployment.png' | relative_url }})

*The four selected audit subcategories were not yet enabled.*

The SMB results were already encouraging: SMBv1 was disabled; SMBv2/3 was enabled; both client and server required SMB signing; and insecure guest logons were disabled.

The Security Event Log, however, had a maximum of only **20 MiB** (`20971520` bytes).

![SMB client and Event Log checks before deployment]({{ '/assets/images/endpoint-security/wf-baseline-smb-client-eventlog-before.png' | relative_url }})

*The SMB client was already hardened, while the Security Event Log still had its smaller limit.*

We discussed taking a powered-off VirtualBox snapshot as a recovery point. The host had only about **30 GB free**, so storage capacity was a concern. **A completed snapshot and a fresh BitLocker recovery-key access check were not evidenced**, and neither is claimed as completed here.

### Assign the Policy and Sync the Device Remotely

Our goal is to manage Wire Finance devices centrally. An employee might be working in another city or country; IT should not need someone physically beside the device to request a policy check-in.

When the baseline was ready, I opened **WF-SEC-WIN-BASELINE → Properties → Assignments**, added **`SG-DEVICES-WINDOWS-PILOT`**, and saved the assignment.

Then, rather than asking Olivia to open Settings on her VM, I used:

**Intune → Devices → Windows → Windows devices → DESKTOP-QDSCPUK → Sync**

The remote sync showed **Notifying device: Completed** and **Policies: 8 of 8 succeeded**. The application and script stages also completed.

![Completed remote sync from Intune]({{ '/assets/images/endpoint-security/wf-baseline-intune-remote-sync-completed.png' | relative_url }})

*The policy check-in was requested and monitored through the Intune portal.*

**A successful sync is not the same as a successful baseline.** It confirms the sync workflow completed; we still need to examine the result for the particular profile.

I returned to **Endpoint security → Security baselines → Security Baseline for Windows 10 and later → Profiles → WF-SEC-WIN-BASELINE** and opened its **Device assignment status** report.

| Assignment status | Count |
|---|---:|
| **Success** | **1** |
| Pending | 0 |
| Not applicable | 0 |
| Error | 0 |
| Conflict | 0 |

![Baseline device assignment succeeded]({{ '/assets/images/endpoint-security/wf-baseline-device-assignment-success.png' | relative_url }})

*Our single pilot reported Success, with no reported errors or conflicts.*

The **Per setting status** report also showed **Succeeded** for the entries we examined, including the auditing settings, SMBv1 restrictions, UAC and Event Log settings. We did not export and independently inspect every row of the full report, so that distinction remains part of our evidence record.

Be careful with the wording of a setting in that report. **Allow Basic authentication — Succeeded** does **not** mean Basic authentication was turned on. It means Intune applied the selected value; ours was **Disabled**.

### Confirm the Auditing Changes on Windows

Intune's success report was encouraging. Now we needed to verify the outcome against the **No Auditing** values we recorded earlier.

On the pilot, I repeated four targeted checks:

```powershell
auditpol /get /subcategory:"Process Creation"
auditpol /get /subcategory:"Credential Validation"
auditpol /get /subcategory:"Group Membership"
auditpol /get /subcategory:"File Share"
```

| Audit subcategory | Before | After |
|---|---|---|
| **Process Creation** | No Auditing | **Success** |
| **Credential Validation** | No Auditing | **Success and Failure** |
| **Group Membership** | No Auditing | **Success** |
| **File Share** | No Auditing | **Success and Failure** |

![Auditing enabled after baseline deployment]({{ '/assets/images/endpoint-security/wf-baseline-auditing-after-deployment.png' | relative_url }})

*Windows confirmed the four auditing changes; BitLocker remained FullyEncrypted with Protection On.*

These commands verified the result; they did not configure the device. **Intune delivered the policy centrally**, while local PowerShell gave us extra evidence for the learning journey.

### Investigate the Event Log Setting That Did Not Initially Take Effect

Not every result matched immediately.

The baseline configured a **192 MiB Security Event Log maximum**, and Intune reported the setting as **Succeeded**. But the first Windows check after deployment still returned **20 MiB**.

This raised a useful question: **Had Intune failed to deliver the setting, or had Windows not yet applied it to the effective log configuration?**

I compared the Windows Event Log configuration with the delivered policy:

```powershell
wevtutil gl Security

Get-ItemProperty `
    -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows\EventLog\Security" `
    -Name MaxSize `
    -ErrorAction SilentlyContinue
```

| Location | Reported value |
|---|---|
| **Delivered policy registry value** | `196608` KB = **192 MiB** |
| **Effective Security log maximum** | `20971520` bytes = **20 MiB** |

![Policy value and effective Event Log size differed]({{ '/assets/images/endpoint-security/wf-baseline-eventlog-policy-vs-effective.png' | relative_url }})

*The intended registry policy was present, but the effective maximum had not changed.*

There was no need to force the setting through PowerShell or edit the registry manually. We realised the VM **had not yet been restarted since deployment**.

After the VM restarted, I repeated the read-only check:

```powershell
Get-WinEvent -ListLog Security |
    Select-Object LogName, MaximumSizeInBytes |
    Format-List
```

```text
LogName            : Security
MaximumSizeInBytes : 201326592
```

![Security Event Log maximum after restart]({{ '/assets/images/endpoint-security/wf-baseline-eventlog-after-restart.png' | relative_url }})

*After the restart, the effective Security log maximum reached **192 MiB**.*

**What did this prove?** In this pilot, the policy was present before the restart, while the effective setting reflected the intended value afterward. It does **not** mean every Event Log policy requires a restart, and we did not capture evidence establishing how that restart was initiated.

### Make Sure Existing Security Policies Still Work

The baseline was never meant to replace our previous endpoint policies. After deployment, I returned to Intune and checked their reported results:

| Existing policy | Status |
|---|---|
| `WF-SEC-WIN-ANTIVIRUS` | **Succeeded** |
| `WF-SEC-WIN-FIREWALL` | **Succeeded** |
| `WF-SEC-WIN-ASR` | **Succeeded** |
| `WF-SEC-WIN-EDR-ONBOARDING` | **Succeeded** |

One report showed **2 Succeeded** under **Device and user check-in status**, even though our pilot contained one Windows VM.

Was a second device being managed? The **View report** link answered that:

| Device | Reporting identity | Result |
|---|---|---|
| `DESKTOP-QDSCPUK` | System account | Success |
| `DESKTOP-QDSCPUK` | Olivia's signed-in account | Success |

![Device and user context records for one pilot]({{ '/assets/images/endpoint-security/wf-baseline-dual-checkin-system-user.png' | relative_url }})

*The user identifier is masked. Both records belong to the same endpoint.*

The count represented **two reporting contexts**, not two machines. We therefore left the working policy unchanged.

### Our Security Baseline Checkpoint

Before moving on, here is what this lesson established:

| Check | Result |
|---|---|
| `WF-SEC-WIN-BASELINE` created | **Configured and saved** |
| Baseline version | **26H2** |
| Initial assignment | **None** — profile reviewed before deployment |
| Pilot targeting | `SG-DEVICES-WINDOWS-PILOT` |
| Remote Intune sync | **Completed** |
| Device assignment | **1 Success, 0 Errors, 0 Conflicts** |
| Four targeted audit subcategories | **Verified on Windows** |
| Security Event Log maximum | **20 MiB → 192 MiB after restart** |
| BitLocker | **FullyEncrypted; Protection On** |
| Antivirus, Firewall, ASR and EDR | **Still reporting Succeeded** |
| Credential Guard, VBS, HVCI and DMA | **Intentionally excluded from this baseline** |
| Windows LAPS | **Not configured here** |

The result is **a verified baseline deployment on one VirtualBox pilot**, not an enterprise-wide compatibility assessment. We have not completed a full recovery rehearsal, tested every baseline setting, or established that all printers and applications would be unaffected across future corporate devices.

For now, the distinction matters: **the profile was configured, Intune delivered it remotely, and the targeted Windows checks showed real security changes**. The Event Log discrepancy also taught us not to confuse a successful report with an already-effective setting.

### Next: Account Protection and Windows LAPS

We've now hardened the first managed Windows endpoint while keeping its earlier security policies healthy.

But securing Windows settings is different from **controlling local administrator credentials**. A shared or unmanaged local administrator password can undermine much of the protection we've configured.

In **Lesson 7**, we'll create a separate Account Protection and Windows LAPS policy, decide how local administrator passwords should be managed and recovered through Microsoft Entra, and test the result on our existing pilot before considering wider rollout.

**Next: Account Protection and Windows LAPS — Lesson 7.**

#### References

- [Microsoft Learn — Security baselines for Windows in Intune](https://learn.microsoft.com/en-us/intune/device-security/security-baselines/overview)
- [Microsoft Learn — Configure security baseline profiles](https://learn.microsoft.com/en-us/intune/device-security/security-baselines/configure-baselines)
- [Microsoft Learn — Monitor security baselines](https://learn.microsoft.com/en-gb/mem/intune/protect/security-baselines-monitor)
- [Microsoft Learn — Windows MDM security baseline settings (26H2)](https://learn.microsoft.com/en-us/intune/device-security/security-baselines/ref-windows-mdm-settings?pivots=mdm-26h2)
- [Microsoft Learn — EventLogService Policy CSP](https://learn.microsoft.com/en-us/windows/client-management/mdm/policy-csp-eventlogservice)
