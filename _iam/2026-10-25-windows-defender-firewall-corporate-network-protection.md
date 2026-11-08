---
layout: lesson
title: "Windows Defender Firewall — Corporate Network Protection"
series: wire-finance
parent: endpoint-security-and-compliance-implementation
order: 3
description: "Configure and verify the Wire Finance Windows Defender Firewall in Intune, including all three network profiles, traffic logging, rule-merging decisions and cloud connectivity checks."
---

BitLocker protects data stored on the device. Microsoft Defender Antivirus helps detect and contain malicious files. **Windows Defender Firewall adds another layer: controlling network traffic entering and leaving the endpoint.**

In this lesson, configure the Wire Finance firewall standard through Microsoft Intune, assign it to the Windows pilot group and verify its effect on the actual device. We will use **default inbound Block**, **default outbound Allow** and a separate log for each network profile. The configuration is designed for a cloud-managed Windows 11 endpoint, not an on-premises Active Directory domain.

> **What is the difference?** A firewall's default inbound *Block* does not mean every inbound packet will be dropped: an applicable allow rule can still permit traffic. Likewise, outbound *Allow* is a default action, not a guarantee that every connection succeeds.

### 1. Confirm the Pilot and Its Existing Firewall State

| Item | Wire Finance value |
|---|---|
| VM asset | `WF-LT-001` |
| Windows hostname | `DESKTOP-QDSCPUK` |
| Pilot user | Olivia Carter — Finance Analyst |
| Intune policy | `WF-SEC-WIN-FIREWALL` |
| Policy assignment | `SG-DEVICES-WINDOWS-PILOT` — **assigned** device group |
| Scope tag | `Default` |

The VM has two VirtualBox network adapters, both currently categorised as **Public**. We will still configure Domain, Private and Public profiles so the policy is consistent when devices move between network types.

Before changing policy, open **Windows PowerShell as Administrator** and check the existing state:

```powershell
Get-NetFirewallProfile -PolicyStore ActiveStore |
    Select-Object Name, Enabled, DefaultInboundAction,
                  DefaultOutboundAction, LogBlocked, LogAllowed,
                  LogMaxSizeKilobytes |
    Format-Table -AutoSize

Get-NetConnectionProfile |
    Select-Object Name, InterfaceAlias, NetworkCategory, IPv4Connectivity

Get-Service -Name MpsSvc
```

On our pilot, all three firewall profiles were already **Enabled**, default inbound traffic was **Block** and outbound was **Allow**. The gap was in central policy enforcement and visibility: dropped and allowed connection logging were both **False**, and the log-size limit was **4,096 KB** per profile.

![Before-policy Domain Private and Public firewall state]({{ '/assets/images/endpoint-security/wf-firewall-before-policy-profiles.png' | relative_url }})

*Record the pre-deployment firewall configuration. Windows was already protecting inbound traffic, but logging had not been enabled.*

![VirtualBox adapters using Public network profiles]({{ '/assets/images/endpoint-security/wf-firewall-before-policy-network-types.png' | relative_url }})

*Ethernet has Internet connectivity; Ethernet 2 has local-network connectivity. Both were Public, so Public-profile logging is especially relevant to this VM.*

The Windows Defender Firewall service, **`MpsSvc`**, also reported **Running**. Do not change these network profiles merely to make the deployment succeed.

![Firewall service running before policy assignment]({{ '/assets/images/endpoint-security/wf-firewall-service-running.png' | relative_url }})

*The Windows Defender Firewall service is running.*

### 2. Create the Firewall Policy in Microsoft Intune

Open the [Microsoft Intune admin center](https://intune.microsoft.com/) and go to:

**Endpoint security → Firewall → Create Policy**

Select **Platform: Windows** and **Profile: Windows Firewall**. On **Basics**, enter:

| Field | Value |
|---|---|
| Name | `WF-SEC-WIN-FIREWALL` |
| Description | `Wire Finance corporate Windows Firewall protection — pilot deployment` |

Select **Next** to open **Configuration settings**.

#### Enable the three network profiles

Set the following **True**:

- **Enable Domain Network Firewall**
- **Enable Private Network Firewall**
- **Enable Public Network Firewall**

Each selection expands a profile-specific set of controls. **Apply the same standard to all three profiles.** The Domain profile is still worth configuring even though this particular Microsoft Entra joined VM currently operates on Public networks.

#### Apply the approved profile settings

Configure the following values under **each** of Domain, Private and Public:

| Intune configuration setting | Wire Finance value |
|---|---|
| Enable network firewall | **True** |
| Default Inbound Action | **Block** |
| Default Outbound Action | **Allow** |
| Disable Stealth Mode | **False** — stealth mode stays enabled |
| Shielded | **False** — do not suppress all inbound allow rules |
| Disable Unicast Responses To Multicast Broadcast | **False** |
| Disable Inbound Notifications | **False** |
| Disable Stealth Mode IPsec Secured Packet Exemption | **True** |
| Enable Log Dropped Packets | **Enable Logging Of Dropped Packets** |
| Enable Log Success Connections | **Enable Logging Of Successful Connections** |
| Enable Log Ignored Rules | **Disable Logging Of Ignored Rules** |
| Log Max File Size | **20480** KB |
| Auth Apps Allow User Pref Merge | **True** |
| Global Ports Allow User Pref Merge | **True** |
| Allow Local Policy Merge | **True** |
| Allow Local IPsec Policy Merge | **True** |

The negatively named **Disable Stealth Mode = False** is intentional. We want stealth-mode behaviour retained, not switched off.

**Why are local rule merges allowed?** This first pilot prioritises application compatibility. True allows eligible locally created rules to remain active, which means the configuration is **not** an exclusively centrally controlled rule set. Before a wider production rollout, audit existing exceptions, document legitimate dependencies, and decide whether to deploy approved allow rules from Intune and restrict local merging. The present lesson does not claim to have completed that hardening decision.

#### Set separate log files

Use the matching **Log File Path** for each profile:

| Profile | Log File Path |
|---|---|
| Domain | `%systemroot%\system32\LogFiles\Firewall\pfirewall_Domain.log` |
| Private | `%systemroot%\system32\LogFiles\Firewall\pfirewall_Private.log` |
| Public | `%systemroot%\system32\LogFiles\Firewall\pfirewall_Public.log` |

Keep **Log Max File Size = `20480`** on each profile. These are local diagnostic logs, **not automatic ingestion into Microsoft Sentinel**. Connection logging has a storage cost; review traffic volume and retention before expanding beyond the pilot.

#### Leave unrelated advanced settings alone

Our standard does not introduce an IPsec authenticated-connection design or a separate corporate TLS network-identification endpoint. Leave advanced items such as **Certificate revocation list verification, IPsec exceptions, stateful FTP changes, packet queues, VM Creator Id / Target, Windows Filtering Platform audit categories, Allowed TLS Authentication Endpoints** and **Configured TLS Authentication Network Name** **Not configured** or empty. These settings are not required simply because the device runs in VirtualBox.

Windows Filtering Platform auditing is distinct from the text-based Windows Firewall logs configured above. High-volume packet auditing should have its own monitoring and retention plan.

### 3. Target Only the Windows Pilot Group

On **Scope tags**, select **Default**, consistent with our existing Intune RBAC design.

On **Assignments**:

| Field | Selection |
|---|---|
| Included groups | `SG-DEVICES-WINDOWS-PILOT` |
| Excluded groups | None |

Confirm that the group contains the intended Entra device object for `DESKTOP-QDSCPUK`. It is an **assigned** group, not the dynamic `SG-DEVICES-AUTOPILOT-CORPORATE` classification group.

Select **Review + create**. Compare the Domain, Private and Public settings, logging paths and group scope before selecting **Create**.

### 4. Verify Policy Delivery in Intune

On Olivia's device page, a manual **Sync** can be useful during lab testing. In a normal production environment, enrolled endpoints check in automatically; administrators do not need to sync every device manually.

![Intune sync completed for five policy operations]({{ '/assets/images/endpoint-security/wf-firewall-intune-sync-complete.png' | relative_url }})

*The device sync reported five policy operations successful. This is an overall sync summary, not by itself proof that the new Firewall policy was applied.*

For the specific policy, navigate to:

**Endpoint security → Firewall → `WF-SEC-WIN-FIREWALL`**

Open **Device and user check-in status → View report**, or generate the **Device assignment status** report if required. Confirm the intended device and investigate any errors or conflicts. Our result was:

| Policy report item | Observed result |
|---|---|
| Successful device check-ins | **1** |
| Error | **0** |
| Conflict | **0** |
| In progress | **0** |
| Intended device | `DESKTOP-QDSCPUK` |

![Intune firewall policy with one successful check-in]({{ '/assets/images/endpoint-security/wf-firewall-policy-success.png' | relative_url }})

*A clean policy report is useful, but endpoint-side checks are still required.*

### 5. Verify the Effective Firewall Settings in Windows

On Olivia's VM, run this in elevated PowerShell:

```powershell
Get-NetFirewallProfile -PolicyStore ActiveStore |
    Format-List Name, Enabled, DefaultInboundAction,
                DefaultOutboundAction, LogBlocked, LogAllowed,
                LogMaxSizeKilobytes, LogFileName
```

The effective result matched our configured values for all **three** profiles:

| Property | Domain | Private | Public |
|---|---|---|---|
| Enabled | True | True | True |
| DefaultInboundAction | Block | Block | Block |
| DefaultOutboundAction | Allow | Allow | Allow |
| LogBlocked | True | True | True |
| LogAllowed | True | True | True |
| LogMaxSizeKilobytes | 20480 | 20480 | 20480 |
| LogFileName | Domain-specific | Private-specific | Public-specific |

![PowerShell output confirming three effective firewall profiles]({{ '/assets/images/endpoint-security/wf-firewall-effective-profiles.png' | relative_url }})

*Direct Windows verification establishes that the effective firewall and log settings match the Intune configuration.*

**Why inspect `ActiveStore`?** It reports the effective configuration, rather than simply repeating values entered into the admin center.

### 6. Verify Firewall Logging and Permissions

Inspect the firewall log directory:

```powershell
Get-ChildItem "$env:windir\System32\LogFiles\Firewall" |
    Select-Object Name, Length, LastWriteTime
```

Initially, Windows created the three named log files but the **Public log was empty**, while Domain and Private contained only small header-sized files. This does not by itself establish a problem: files can be created before there are qualifying records.

![Firewall log directory after policy deployment]({{ '/assets/images/endpoint-security/wf-firewall-log-files-initial.png' | relative_url }})

*File presence is not the same as evidence of logged traffic.*

Check the directory's permissions **without altering them**:

```powershell
$LogPath = "$env:windir\System32\LogFiles\Firewall"
(Get-Acl -Path $LogPath).Access |
    Select-Object IdentityReference, FileSystemRights,
                  AccessControlType |
    Format-Table -AutoSize
```

On our VM, `NT SERVICE\mpssvc` had **FullControl — Allow**, alongside `SYSTEM` and `Administrators`. This supports the service's ability to write log files.

![Firewall folder ACL showing mpssvc service access]({{ '/assets/images/endpoint-security/wf-firewall-log-permissions.png' | relative_url }})

To inspect actual records, use:

```powershell
Get-Content "$env:windir\System32\LogFiles\Firewall\pfirewall_Public.log" -Tail 25
```

Our evidence contains **`ALLOW TCP`**, **`ALLOW UDP`**, **`ALLOW ICMP`** and **`DROP UDP`** records. The UDP drops include destination port `1900`, commonly associated with SSDP discovery; a dropped network-discovery packet does not necessarily indicate an attack.

![Public firewall log showing ALLOW and DROP entries]({{ '/assets/images/endpoint-security/wf-firewall-allow-drop-evidence.png' | relative_url }})

*Source and destination IP addresses are masked for public sharing. Real traffic records demonstrate that logging operated. The displayed entries were dated 26 October 2026; they are **not** presented as proof that a later host-to-VM ping generated those records.*

The logging check establishes that traffic has been recorded. It does **not** separately prove that a particular test packet was dropped unless the source, destination and time can be matched.

### 7. Confirm Microsoft Cloud Connectivity

From the enrolled VM, verify that it can establish TCP connections to representative identity and Intune web endpoints:

```powershell
Test-NetConnection login.microsoftonline.com -Port 443
Test-NetConnection intune.microsoft.com -Port 443
```

Our results for **both** endpoints were:

```text
RemotePort      : 443
InterfaceAlias  : Ethernet
SourceAddress   : [private VM address redacted]
TcpTestSucceeded: True
```

![Successful TCP port 443 probes to Microsoft sign-in and Intune]({{ '/assets/images/endpoint-security/wf-firewall-microsoft-connectivity-pass.png' | relative_url }})

*Microsoft sign-in and Intune HTTPS endpoints were reachable via Olivia's NAT-connected Ethernet adapter. IP addresses are masked for public sharing.*

These tests verify **TCP reachability only**. They do not independently establish successful interactive sign-in, every Microsoft 365 application's functionality, or the full Intune service endpoint list. Confirm business application behaviour separately before widening the scope.

### 8. Common Problems and Solutions

| Symptom | What to check | Appropriate response |
|---|---|---|
| Firewall policy is pending or missing from a report | Assignment group membership and report generation | Verify the **assigned** pilot group; select **Generate report** when needed, then check the device report |
| Intune reports success but logging remains off | The effective `ActiveStore` profile | Review the configured profile and **Per setting status**; do not assume a green policy result proves logging is active |
| Log file exists but is empty | Active network profile, qualifying traffic and timestamps | Check the correct profile; permit normal traffic generation; an empty file alone does not mean logging failed |
| No log is written despite qualifying traffic | Folder permissions for `NT SERVICE\mpssvc`, log path and disk access | Inspect the ACL and correct only a verified permission or path issue |
| Local app networking behaves differently | Default actions, local rule merging, inbound rule requirements | Identify a specific required rule and manage it deliberately; **don't disable the firewall** as a workaround |
| HTTPS connectivity fails | DNS, routing, NAT/bridged networking, proxy and effective firewall rules | Distinguish a network-path problem from a firewall policy problem before changing security settings |

Avoid opening unnecessary inbound ports merely to make a test pass. Our policy allows legitimate outbound connections by default; more restrictive outbound control and centrally governed exception rules can be added when requirements are established and validated.

### 9. Outcome and Next Lesson

We now have a **centrally managed, verified Windows Defender Firewall baseline** on `DESKTOP-QDSCPUK`:

- Intune reported **one successful policy check-in**, zero errors and zero conflicts.
- Windows confirmed all three profiles **Enabled**, default inbound **Block**, default outbound **Allow** and firewall logging configured.
- Separate log files were created, firewall service write permissions were confirmed, and the Public log contained genuine **ALLOW/DROP** records.
- HTTPS connections to the Microsoft sign-in and Intune endpoints returned **`TcpTestSucceeded: True`**.

**Scope and limits:** This validates the configured pilot firewall policy and representative network connectivity, not every rule exception, every Microsoft 365 workflow, or enterprise-wide performance. The pilot still permits locally created firewall rules, and that governance decision will be revisited before wider rollout.

**Next: Attack Surface Reduction — audit, evaluation and controlled enforcement.**

#### Microsoft reference material

- [Firewall policy settings for endpoint security in Intune](https://learn.microsoft.com/en-us/intune/device-configuration/endpoint-security/ref-firewall-settings)
- [Configure Windows Firewall logging](https://learn.microsoft.com/en-us/windows/security/operating-system-security/network-security/windows-firewall/configure-logging?tabs=intune)
- [Windows Firewall CSP reference](https://learn.microsoft.com/en-us/windows/client-management/mdm/firewall-csp)
