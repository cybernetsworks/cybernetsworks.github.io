---
layout: lesson
title: "Post-Deployment Validation"
series: wire-finance
parent: endpoint-device-management-implementation
order: 9
description: "Verify the first Wire Finance Windows Autopilot pilot across Windows, Microsoft Entra ID, Intune and application reporting before moving from successful onboarding to endpoint security and compliance."
---

Olivia Carter has reached the Windows desktop on our first corporate pilot device. Company Portal is installed, and its device page identifies `DESKTOP-QDSCPUK` with the message **Can access company resources**.

That's a successful first onboarding experience. But there's one more question before we close this stage of the project:

**Can we prove, from the management portals, that the right device was joined, enrolled, assigned to Olivia and given the expected applications?**

A desktop that opens isn't enough evidence on its own. In this final lesson, we'll check the device across Windows, Microsoft Entra ID, Windows Autopilot and Microsoft Intune, then record any gaps before moving into endpoint security.

### 1. Know What We're Validating

Keep the distinction between our **asset name**, **Windows hostname** and **cloud records** clear:

| Identifier or object | Wire Finance reference | Why it matters |
|---|---|---|
| Asset / VirtualBox VM | `WF-LT-001` | Our internal reference for the first test workstation |
| Windows computer name | `DESKTOP-QDSCPUK` | The hostname produced during setup; the Autopilot naming template is disabled |
| Autopilot record | VirtualBox hardware registration with Group Tag `CORPORATE` | Shows that Autopilot recognises and classifies the hardware |
| Microsoft Entra device record | Device identity created for the Windows join | Shows the device's directory identity and join state |
| Intune managed-device record | Device management object created during enrollment | Shows management, primary user, check-in and application reports |

The **Autopilot registration record isn't the same as a completed Intune enrollment**. Also, Autopilot can create an Entra device object before the employee signs in. That's why we need to inspect *join type*, *management state* and the actual Intune record—not merely find a matching name.

Our recorded first-pilot result is **`WF-ENDPOINT-PILOT-001` — core provisioning successful**. The detailed portal checks in this lesson are the next validation exercise; we won't mark them complete until we've seen their actual results.

### 2. Confirm the Device Identity in Windows

Start with the VM itself. Sign in to `WF-LT-001` as Olivia, then open **Command Prompt** and run:

```cmd
hostname
dsregcmd /status
```

The `hostname` command should display the computer name seen during our first deployment:

```text
DESKTOP-QDSCPUK
```

In the **Device State** section of `dsregcmd /status`, look for the values appropriate to our cloud-only model:

```text
AzureAdJoined  : YES
DomainJoined   : NO
```

Where reported, **DeviceAuthStatus: SUCCESS** provides another useful directory-authentication signal. Copy the **DeviceId** into your private validation notes so we can compare it with the Microsoft Entra device's **Device ID**. It is **not** the same as the Intune managed-device ID.

**Record the actual output before declaring a pass.** We have not yet saved a verified `dsregcmd /status` result for this post-deployment checkpoint. Avoid publishing the full command output: it can contain tenant identifiers, account details and other diagnostic information.

> `dsregcmd /status` is an additional Windows-side identity check. It does not replace confirming Intune management or installed applications in the admin center.

### 3. Verify the Device in Microsoft Entra ID

Open the [Microsoft Entra admin center](https://entra.microsoft.com/) and navigate to:

**Entra ID → Devices → All devices**

Search for `DESKTOP-QDSCPUK`, then open the matching device. If there are multiple similar entries, compare the **Device ID** with the value collected from Windows rather than relying on the display name alone.

Review these fields:

| Property | What to check |
|---|---|
| **Display name** | The Windows hostname or the matching device identity |
| **Join type** | **Microsoft Entra joined**, not merely Microsoft Entra registered |
| **Enabled** | **Yes** |
| **MDM** | **Microsoft Intune** where reported |
| **Owner** | Inspect the association with Olivia; don't assume it matches Intune primary user automatically |
| **Device ID** | Matches `DeviceId` from Windows |

The join type is the critical check. **Finding a device object in Entra does not, by itself, prove the user-driven join completed**, because the Autopilot registration process can create a pre-deployment device object.

Capture a redacted screenshot of the relevant fields once you've verified them. The original Autopilot group-membership screenshot from Lesson 7 is **not** a substitute for this post-deployment check.

### 4. Verify the Intune Managed-Device Record

Open the [Microsoft Intune admin center](https://intune.microsoft.com/) and follow:

**Devices → All devices → DESKTOP-QDSCPUK**

You can also narrow the list under **Devices → Windows** if the portal presents a Windows device view.

Check **Overview**, **Properties** and **Device details**. The current Intune interface groups some of these details into tabs, so look for the field names if your layout differs.

| Field | Expected check for our pilot |
|---|---|
| **Device name** | `DESKTOP-QDSCPUK` |
| **Operating system** | Windows 11 / Windows |
| **Management** | Managed by **Microsoft Intune** |
| **Ownership** | Review the reported ownership; our intended model is corporate |
| **Primary user** | **Olivia Carter** |
| **Last check-in** | A populated, reasonably recent timestamp |
| **Microsoft Entra device ID** | Corresponds to the verified Entra device identity |
| **Intune device ID** | Present; record separately from the Entra device ID |
| **Enrollment profile / enrolled by** | Examine what Intune reports for this enrollment |
| **Compliance** | Record the actual status; **do not assume compliant means hardened** |

Two details deserve attention:

**Primary user versus enrolled by.** User-driven Autopilot normally assigns the enrolling user as Intune **primary user**. That should be Olivia here, but we still need to verify it. The **Enrolled by** field and Entra **Owner** are related concepts, not guaranteed interchangeable values. If the primary user is unexpected, investigate before editing it.

**Enrollment time.** Microsoft's device-details guidance notes that the **Enrolled date** field for a Windows Autopilot device can reflect its *Autopilot registration time* rather than the precise time it completed Intune enrollment. Use the managed-device record and check-in/reporting signals together rather than relying on one timestamp.

If the Intune device doesn't appear yet, confirm you're searching for the **Windows hostname**, not `WF-LT-001`. Check that the VM is connected and signed in, then allow the portal to refresh. Don't delete or re-register the Autopilot record as a first troubleshooting step.

### 5. Check the Autopilot Record After Deployment

Return to:

**Devices → Device onboarding → Enrollment → Windows Autopilot → Devices**

Open the `innotek GmbH` / `VirtualBox` record registered in Lesson 7. If necessary, identify it using the recorded hardware identifier and `CORPORATE` Group Tag—not the generated Windows hostname alone.

Confirm the following:

| Field | Expected result |
|---|---|
| **Manufacturer / model** | `innotek GmbH` / `VirtualBox` |
| **Group Tag** | `CORPORATE` |
| **Profile status** | **Assigned** |
| **Assigned profile** | `WF_AP_CORPORATE_USER_DRIVEN` |
| **Enrollment status** | Check whether it now reports enrollment complete |
| **Associated Intune device** | Check for a corresponding managed-device association or link |

Before Olivia's sign-in, this record correctly showed **Not enrolled** and **Associated Intune device: N/A**. Here we're checking whether that changed after the deployment. **We haven't recorded the post-deployment Autopilot values yet**, so don't insert a successful result without viewing the updated record.

Note that the optional **Autopilot device-to-user preassignment** we skipped earlier is not the same thing as Intune's **primary user**. An unassigned Autopilot user field does not automatically mean the Intune primary user is missing.

### 6. Verify Both Application Deployments

Company Portal was installed during the first deployment, while **Microsoft 365 Apps - Wire Finance Pilot** was initially shown as **Download pending**. Our next task is to verify the finished results in Intune rather than assume every Office app is installed.

Open:

**Apps → All apps → Company Portal → Monitor → Device install status**

Find `DESKTOP-QDSCPUK` and inspect the installation status. For Company Portal, we already have supporting employee-side evidence from Lesson 8: it opened and recognised the device.

Repeat for:

**Apps → All apps → Microsoft 365 Apps - Wire Finance Pilot → Monitor → Device install status**

Also inspect **User install status** for Olivia, because this suite was assigned to **`SG-PILOT-IDENTITY`** as a Required *user* deployment.

| Application | Our original assignment | What we must establish |
|---|---|---|
| Company Portal | **Required / System** → `SG-DEVICES-AUTOPILOT-CORPORATE` | Installed for the VM and reported accordingly |
| Microsoft 365 Apps | **Required** → `SG-PILOT-IDENTITY` | Installation finished without failure and the intended apps are present |

The intended six-app productivity selection is **Word, Excel, PowerPoint, Outlook, OneNote and Teams**, using **64-bit Current Channel**. Check the Intune deployment result and inspect the installed applications on the Windows device as a second signal.

Seeing Outlook and Teams icons isn't proof that the entire suite has installed. Similarly, seeing **no Available apps** in Company Portal is expected because our initial assignments are **Required**, not Available.

If the Office deployment remains pending or fails, review its actual installation status and error details; confirm the user assignment, licensing, network access and device check-in before retrying. There is no reason to block ESP on the whole suite: **Company Portal remains our only selected ESP-blocking app**.

### 7. Record Compliance Without Mistaking It for Security Approval

We deliberately have **not** completed the Windows endpoint security baseline, BitLocker policy, Defender for Endpoint onboarding, or a formal device-compliance policy for this pilot.

So if Intune displays **Compliant**, don't treat that word as proof that these controls were deployed. Intune has a tenant-level setting, **Mark devices with no compliance policy assigned as**, which may consider devices compliant even without a dedicated compliance policy.

When we implement compliance, we'll review this setting at:

**Endpoint security → Device compliance → Compliance policy settings**

For now, capture the reported compliance value and identify which policies—if any—were actually evaluated. **A successful Autopilot deployment is an onboarding milestone, not a production-security sign-off.**

### 8. Record Our Validation Results

Use the following table as the final evidence checklist. Only mark **Verified** after checking the relevant portal or device screen, and record a date, screenshot and any discrepancy in your working notes.

| Verification | Current evidence / action |
|---|---|
| Olivia reached the corporate Windows desktop | **Observed — Lesson 8** |
| Company Portal installed and opened | **Observed — Lesson 8** |
| Company Portal showed **Can access company resources** | **Observed — Lesson 8** |
| Windows hostname `DESKTOP-QDSCPUK` | **Observed — Lesson 8** |
| Windows `dsregcmd` join state | **Verify now** |
| Entra joined device record and Device ID | **Verify now** |
| Intune managed-device record and last check-in | **Verify now** |
| Intune primary user = Olivia Carter | **Verify now** |
| Post-deployment Autopilot enrollment / Intune association | **Verify now** |
| Microsoft 365 Apps full installation | **Verify now — previously Download pending** |
| Endpoint hardening, Defender, compliance and Conditional Access enforcement | **Not yet implemented or validated** |

For a public write-up, use **redacted screenshots** from the actual Windows, Entra, Intune, Autopilot and app-report views once these checks are completed. Don't substitute the pre-deployment registration images for post-deployment results or expose account names, device IDs and tenant identifiers unnecessarily.

### Common Discrepancies and What to Check

| What you see | First place to investigate |
|---|---|
| No Entra joined record | Verify `dsregcmd /status`, the tenant and the Entra device list; don't confuse the earlier Autopilot-created object with successful join |
| No Intune device under `WF-LT-001` | Search for **`DESKTOP-QDSCPUK`**, the actual Windows hostname |
| Wrong or missing primary user | Open the Intune device's **Overview / Properties** and compare with Olivia's user-driven sign-in; investigate before changing ownership |
| Last check-in is stale | Confirm Windows connectivity and management status; use the supported **Sync** action if appropriate and review the resulting check-in |
| Autopilot still shows Not enrolled or no associated Intune device | Compare the Autopilot record with the live Intune device record; allow status propagation and investigate mismatched identifiers |
| Microsoft 365 Apps still pending | Check app **Device install status**, **User install status** and installation details for the first real error |
| Device displays Compliant without security policies | Inspect the tenant's **no compliance policy assigned** setting; don't claim security controls have passed |

### Where We Stand at the End of Chapter 8

We've established that a fresh Windows 11 Pro VM can be recognised by Windows Autopilot, receive the Wire Finance corporate provisioning experience and reach a usable employee desktop. Company Portal installed successfully, and the Microsoft 365 Apps deployment began automatically.

That is a meaningful milestone: **`WF-ENDPOINT-PILOT-001` — core provisioning successful**.

Our next responsibility is to finish and document the **post-deployment portal checks** above. Until those results have been captured, we should not label this endpoint fully validated or production-ready.

Once the evidence matches our intended design, we'll move into the next implementation stage: **Windows endpoint hardening, BitLocker, Microsoft Defender for Endpoint, device compliance and eventually Conditional Access**. Those protections will give the managed endpoint a measurable security posture—not just an organisational identity.

---

**Microsoft references:** [View device details in Intune](https://learn.microsoft.com/en-us/intune/device-management/inventory-and-status/device-details) · [Find the primary user](https://learn.microsoft.com/en-us/intune/device-management/inventory-and-status/find-primary-user) · [Manage devices in Entra](https://learn.microsoft.com/en-us/azure/active-directory/devices/device-management-azure-portal) · [Monitor app assignments](https://learn.microsoft.com/en-us/intune/app-management/monitor-assignments) · [Troubleshoot device join with dsregcmd](https://learn.microsoft.com/en-us/entra/identity/devices/troubleshoot-device-dsregcmd) · [Device compliance overview](https://learn.microsoft.com/en-us/intune/device-security/compliance/overview)