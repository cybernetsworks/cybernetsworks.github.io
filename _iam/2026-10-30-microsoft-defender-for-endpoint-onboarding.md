---
layout: lesson
title: "Microsoft Defender for Endpoint Onboarding"
series: wire-finance
parent: endpoint-security-and-compliance-implementation
order: 5
description: "Onboard the Wire Finance Windows pilot to Microsoft Defender for Endpoint through Intune, verify sensor health and Advanced Hunting telemetry, and investigate and resolve an authorised antivirus test alert."
---

Our ASR pilot is now in place. We have verified how the configured rules behave on Windows and confirmed that Intune is delivering the policy.

But local protection raises the next question: **if suspicious activity happens on that endpoint, can our security team see what happened without opening the VM?**

Now we bring the same pilot device into **Microsoft Defender for Endpoint (MDE)**. We'll connect the services, onboard the endpoint, trace a harmless process through Advanced Hunting and investigate a controlled security alert.

### 1. Confirm the starting point

Microsoft Defender Antivirus already protects our Windows endpoint. Defender for Endpoint adds centralised detection, process telemetry and investigation capability. **The new onboarding policy does not replace our existing Antivirus, Firewall or ASR configuration.**

| Decision | Wire Finance configuration |
|---|---|
| Policy | `WF-SEC-WIN-EDR-ONBOARDING` |
| Platform / profile | Windows / Endpoint detection and response |
| Onboarding package | Auto from connector |
| Assignment | `SG-DEVICES-WINDOWS-PILOT` |
| Scope tag | Default |
| Exclusions | None |
| First validation device | `DESKTOP-QDSCPUK` (`WF-LT-001`) |

As with our earlier endpoint policies, this is a **corporate configuration tested through the pilot group**. We avoided the quick-deployment option that could target all applicable Windows devices before validation.

### 2. Establish the Defender–Intune connection

We opened **Intune admin center → Endpoint security → Microsoft Defender for Endpoint**. Its connection status was **Unavailable** and no successful synchronisation was reported.

To enable the service-to-service connection, we opened the Microsoft Defender portal. The navigation in our tenant was:

**Setup & configuration → Settings → Endpoints → Optional features → Microsoft Intune connection**

One navigation detour was worth recording: **Endpoints** wasn't initially shown on the Settings landing page. The direct link still worked:

[Defender for Endpoint integration settings](https://security.microsoft.com/securitysettings/endpoints/integration)

We switched **Microsoft Intune connection** to **On**, selected **Save preferences** and returned to Intune. After refreshing, the connector status changed to **Available**, with a successful synchronisation timestamp.

![Intune reports that the Defender for Endpoint connection is Available]({{ '/assets/images/endpoint-security/wf-edr-intune-connection-available.png' | relative_url }})

We left the additional toggles for **Defender security settings enforcement**, **Windows compliance integration** and mobile integration **Off**. Connecting the services does not require us to enable those broader controls or deploy EDR to every device.

> **Navigation tip:** Microsoft documentation may refer to **Advanced features**, while our tenant displayed **Optional features**. Record the path shown in your own portal and retain the direct link as a fallback.

### 3. Capture a baseline before onboarding

Before we created the policy, we opened PowerShell as Administrator on the pilot VM and checked the `Sense` service:

```powershell
Get-Service -Name Sense |
    Select-Object Name, DisplayName, Status, StartType
```

It reported **Stopped** and **Manual**. We then checked the local onboarding flag:

```powershell
Get-ItemProperty `
    -Path "HKLM:\SOFTWARE\Microsoft\Windows Advanced Threat Protection\Status" `
    -Name OnboardingState `
    -ErrorAction SilentlyContinue |
    Select-Object OnboardingState
```

This returned **no value**. That's compatible with an endpoint not yet onboarded, although an absent value by itself is not a complete diagnostic result.

![PowerShell baseline before Microsoft Defender for Endpoint onboarding]({{ '/assets/images/endpoint-security/wf-edr-pre-onboarding-baseline.png' | relative_url }})

We didn't change service settings manually. We kept the results for a before-and-after comparison.

### 4. Create the targeted EDR policy

Back in Intune, we opened **Endpoint security → Endpoint detection and response → Create policy**, selected **Windows** and the **Endpoint detection and response** profile, then named the policy `WF-SEC-WIN-EDR-ONBOARDING`.

The configuration was deliberately simple:

| Setting | Selection |
|---|---|
| MDE client configuration package type | **Auto from connector** |
| Sample Sharing | Not configured |
| Deprecated Telemetry Reporting Frequency | Not configured |

Selecting **Auto from connector** automatically populated the **Onboarding blob from Connector** field. We did not edit, export or publish its contents.

![Intune EDR onboarding policy set to Auto from connector]({{ '/assets/images/endpoint-security/wf-edr-policy-auto-from-connector.png' | relative_url }})

For **Assignments**, we included only `SG-DEVICES-WINDOWS-PILOT`, used the `Default` scope tag and left exclusions empty. We reviewed the settings and created the policy.

**Why leave Sample Sharing unconfigured?** We chose not to impose that preference from this onboarding policy. *Not configured* does not mean file sample submission is necessarily disabled; that decision belongs in a separate review of our Defender configuration and data-handling requirements.

### 5. Verify policy delivery in Intune

After creation, we initiated a management sync from **Devices → Windows → Windows devices → DESKTOP-QDSCPUK → Sync**.

The sync completed successfully. However, a completed device sync is not by itself proof that one specific security policy succeeded, so we checked `WF-SEC-WIN-EDR-ONBOARDING` directly.

| Policy check-in status | Count |
|---|---:|
| Succeeded | **1** |
| Error | 0 |
| Conflict | 0 |
| Not applicable | 0 |
| In progress | 0 |

![Intune EDR policy deployment status with one success and zero failures]({{ '/assets/images/endpoint-security/wf-edr-intune-policy-success.png' | relative_url }})

The **EDR Onboarding Status** report also showed **1 of 1 Windows devices onboarded**. It listed `DESKTOP-QDSCPUK` as Intune-managed with a Defender sensor state of **Yes**.

![Intune EDR Onboarding Status reports the pilot device as onboarded]({{ '/assets/images/endpoint-security/wf-edr-intune-onboarding-status.png' | relative_url }})

### 6. Verify onboarding on Windows and in Defender

We repeated the same two PowerShell checks on the VM after policy delivery.

| Local check | Before | After |
|---|---|---|
| `Sense` status | Stopped | **Running** |
| `Sense` startup type | Manual | **Automatic** |
| `OnboardingState` | No value returned | **1** |

![PowerShell showing the Sense service running and onboarding state equal to one]({{ '/assets/images/endpoint-security/wf-edr-local-sensor-onboarded.png' | relative_url }})

We then opened **Microsoft Defender portal → Assets → Devices**, selected `desktop-qdscpuk` and reviewed its cloud details. The portal reported:

- **Onboarding status:** Onboarded
- **Health state:** Active
- **Managed by:** Intune
- **Operating system:** Windows 11 64-bit
- **Security operations:** Full

![Redacted Defender device details showing Active health and Onboarded status]({{ '/assets/images/endpoint-security/wf-edr-defender-cloud-health-redacted.png' | relative_url }})

The three checks now agreed: **Intune policy delivery, Windows sensor state and Defender cloud registration**. The portal's *last seen* timestamp gave us another reporting checkpoint.

### 7. Prove process telemetry reaches Advanced Hunting

Being onboarded is important, but we also wanted to retrieve a real event that originated on the VM.

We ran a harmless command in PowerShell:

```powershell
cmd.exe /c "echo WF_EDR_TELEMETRY_CHECK"
```

Then we opened **Investigation & response → Hunting → Advanced hunting** and searched for that process-creation event:

```kql
DeviceProcessEvents
| where Timestamp > ago(7d)
| where DeviceName contains "QDSCPUK"
| where ProcessCommandLine contains "WF_EDR_TELEMETRY_CHECK"
| project Timestamp, DeviceName, FileName,
          ProcessCommandLine, InitiatingProcessFileName
| order by Timestamp desc
```

The first queries came back empty. Broader checks initially showed older events, so we investigated the time range and waited for newer data rather than changing security settings or assuming that onboarding had failed.

When we checked again, **the test event appeared**:

| Field | Recorded value |
|---|---|
| Device | `desktop-qdscpuk` |
| Process | `cmd.exe` |
| Initiating process | `powershell.exe` |
| Command line | `"cmd.exe" /c "echo WF_EDR_TELEMETRY_CHECK"` |
| Event timestamp shown | 3 November 2026, 12:59:33 AM |

![Advanced Hunting result showing the harmless test command and parent process]({{ '/assets/images/endpoint-security/wf-edr-advanced-hunting-process-telemetry.png' | relative_url }})

We also confirmed that newer device logs had started appearing.

**Takeaway:** A device appearing in Defender inventory, a healthy sensor and a successful process-event query are different checks. Validate all three.

### 8. Generate and investigate our first test alert

Once the process event was visible, we followed [Microsoft's antivirus validation guidance](https://learn.microsoft.com/en-us/defender-endpoint/validate-antimalware) and used the benign **EICAR** test file on our pilot VM.

Defender Antivirus identified the test signature and **prevented** the file. In **Investigation & response → Incidents & alerts → Alerts**, we found:

| Alert field | Result |
|---|---|
| Alert | `'EICAR_Test_File' malware was prevented` |
| Detection source | **Antivirus** |
| Category | Malware |
| Severity | Informational |
| Device | `desktop-qdscpuk` |
| Result | **Prevented** |
| Date | 4 November 2026 |

The alert story exposed helpful investigation context, including the process chain **`userinit.exe` → `explorer.exe` → `Notepad.exe` → `EICAR.txt`**.

![Redacted Defender EICAR antivirus alert process tree]({{ '/assets/images/endpoint-security/wf-edr-eicar-alert-story-redacted.png' | relative_url }})

This distinction matters: **EICAR verified antivirus prevention and cloud alert reporting, not a dedicated behavioural EDR attack simulation.** The separate Advanced Hunting exercise verified process telemetry from the endpoint.

### 9. Classify and close the authorised test

We intentionally generated this EICAR alert. Defender correctly recognised the test file, so calling the detection a *false positive* would be misleading.

In the alert's management view, we selected **Informational, expected activity** and marked the alert **Resolved**.

![Defender alert showing the resolved and expected-activity disposition]({{ '/assets/images/endpoint-security/wf-edr-eicar-alert-resolved-redacted.png' | relative_url }})

For the first time in this deployment journey, we had followed an endpoint test through **detection → investigation → classification → resolution**.

### 10. Implementation results and what comes next

| Checkpoint | Result |
|---|---|
| Defender–Intune service connection | Verified: Available |
| Pilot EDR policy | Created and deployed |
| Intune policy status | 1 succeeded, 0 errors/conflicts |
| Local sensor / registry | `Sense` running; `OnboardingState = 1` |
| Defender device health | Active; onboarded |
| Advanced Hunting process telemetry | Verified |
| EICAR antivirus prevention and cloud alert | Verified |
| Authorised test alert | Classified and resolved |
| Dedicated behavioural EDR simulation | **Not performed** |

With Defender for Endpoint onboarded, we can now see more than whether a protection rule ran. We retrieved real process telemetry through Advanced Hunting, investigated a prevented antivirus test alert, and recorded its expected-activity resolution. That gives our security team a working path from **endpoint activity to a remotely investigated alert**—without claiming we performed a behavioural EDR attack simulation.

But visibility alone doesn't establish a consistent Windows configuration. **Next: Windows Security Baseline and Hardening.** We'll review Microsoft's security recommendations against the BitLocker, Antivirus, Firewall and ASR controls we've already deployed, account for the limitations of our VirtualBox pilot, and use Intune to apply and verify a carefully scoped baseline.

Device-risk compliance, broader rollout, automated investigation decisions and Microsoft Sentinel integration still belong to later stages. For now, we'll strengthen the same pilot and prove the hardening settings actually take effect.

---

#### References

- [Microsoft Learn — Deploy endpoint detection and response policies with Intune](https://learn.microsoft.com/en-us/intune/device-configuration/endpoint-security/deploy-edr)
- [Microsoft Learn — Advanced Hunting overview](https://learn.microsoft.com/en-us/defender-xdr/advanced-hunting-overview)
- [Microsoft Learn — Validate Microsoft Defender Antivirus](https://learn.microsoft.com/en-us/defender-endpoint/validate-antimalware)
- [Microsoft Learn — Investigate alerts in Microsoft Defender XDR](https://learn.microsoft.com/en-us/defender-xdr/investigate-alerts)
