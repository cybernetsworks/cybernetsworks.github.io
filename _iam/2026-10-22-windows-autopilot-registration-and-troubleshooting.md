---
layout: lesson
title: "Windows Autopilot Registration and Troubleshooting"
series: wire-finance
parent: endpoint-device-management-implementation
order: 7
description: "Use a clear, verified PowerShell procedure to register the Wire Finance Windows 11 VM with Autopilot, apply its CORPORATE Group Tag, verify dynamic membership and profile assignment, and resolve common registration errors."
---

Our Windows 11 Pro virtual machine, `WF-LT-001`, is ready at the first **Out-of-Box Experience (OOBE)** region screen. In earlier lessons, we prepared the Intune enrollment settings, corporate Enrollment Status Page, Autopilot deployment profile and application assignments.

Now we need to connect the virtual device to those configurations.

**In this lesson, you'll register the VM with Windows Autopilot, verify its corporate classification and confirm that the correct deployment profile is assigned—all before Olivia signs in.** We'll use the working registration procedure first. If anything fails, a separate troubleshooting section at the end covers common registration and sign-in errors.

### 1. Understand the Registration Checkpoint

Autopilot registration associates a device's hardware identity with Wire Finance. The **Group Tag** tells our dynamic group which provisioning configuration should apply.

```text
Windows 11 VM at OOBE
          ↓
Upload hardware identity with Group Tag CORPORATE
          ↓
SG-DEVICES-AUTOPILOT-CORPORATE
          ↓
WF_AP_CORPORATE_USER_DRIVEN assigned
          ↓
Ready for Olivia's first sign-in (Lesson 8)
```

**Registration isn't enrollment.** At the end of this lesson, the Autopilot record can correctly show **Not enrolled**. Microsoft Entra join and Intune enrollment take place during the employee deployment in Lesson 8.

Before you begin, confirm that the VM has outbound internet access, the profile `WF_AP_CORPORATE_USER_DRIVEN` is assigned to `SG-DEVICES-AUTOPILOT-CORPORATE`, and an authorised **Intune Administrator** account is available for the online upload. Do not use Olivia's standard employee account for the registration operation.

### 2. Open Windows PowerShell from OOBE

1. Start `WF-LT-001` in Oracle VirtualBox and leave Windows at the **country/region** screen.
2. Press **Shift + F10** to open Command Prompt. Depending on your keyboard, you may need **Fn + Shift + F10**.
3. Enter `powershell` and press **Enter**.
4. Keep OOBE open. **Do not create a local user or sign in as Olivia.**

You'll now work from Windows PowerShell within the VM.

![Windows PowerShell opened from the first OOBE screen in VirtualBox]({{ '/assets/images/endpoint-management/autopilot-oobe-powershell.png' | relative_url }})

### 3. Prepare PowerShell and Install the Script

Run the following commands **in order**. They configure the current session, make sure PowerShell Gallery is registered, locate the published Autopilot script, and install it.

```powershell
# Use TLS 1.2 in this Windows PowerShell session.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

# Allow the script in this session, without changing machine-wide policy.
Set-ExecutionPolicy -Scope Process -ExecutionPolicy RemoteSigned

# Register PSGallery only if it is missing.
if (-not (Get-PSRepository -Name PSGallery -ErrorAction SilentlyContinue)) {
    Register-PSRepository -Default
}

# Check that the repository and script are available.
Get-PSRepository
Find-Script -Name Get-WindowsAutopilotInfo

# Install the script.
Install-Script -Name Get-WindowsAutopilotInfo -Force
```

If prompted to install the **NuGet provider** or approve the expected PowerShell Gallery source, review the prompt and accept it. `Get-PSRepository` should list **PSGallery**, and `Find-Script` should locate `Get-WindowsAutopilotInfo`.

![PSGallery listed by Get-PSRepository]({{ '/assets/images/endpoint-management/autopilot-psgallery-verified.png' | relative_url }})

![Get-WindowsAutopilotInfo discovered through PowerShell Gallery]({{ '/assets/images/endpoint-management/autopilot-find-script-verified.png' | relative_url }})

**Why check the repository first?** The `Install-Script` command can't find packages when PowerShell has no registered repository. Preparing it before installation avoids a common failure.

### 4. Register the VM Directly with Windows Autopilot

Execute the installed script using the full path that worked in this Windows PowerShell environment:

```powershell
& "C:\Program Files\WindowsPowerShell\Scripts\Get-WindowsAutopilotInfo.ps1" -GroupTag CORPORATE -Online
```

| Part | Purpose |
|---|---|
| `&` | Runs the script at the specified path using PowerShell's call operator |
| `-GroupTag CORPORATE` | Classifies the device for our corporate Autopilot group |
| `-Online` | Signs in to Microsoft services and uploads the hardware identity directly |

We're using the **direct online method** rather than first exporting the hardware hash to a CSV file. If your PowerShell installation places the script elsewhere, check its installed location and invoke that path instead.

The command may install or load Microsoft Graph dependencies during its first run. You might also see a NuGet provider prompt; accept it only after checking the expected source. Allow those operations to finish before judging the result.

### 5. Complete the Administrative Sign-In

When the Microsoft authentication window appears:

1. Choose **Work or school account** if the account picker offers personal and organisational accounts.
2. Sign in with the authorised **Wire Finance administrative account** used to perform the registration—not Olivia's everyday account.
3. Review any Microsoft Graph permission request. In our lab, **Consent on behalf of your organisation** was left **unchecked**; only approve permissions your tenant allows and your administrator has authorised.
4. If Windows asks whether to sign in to all apps and websites on the VM, select **No, this app only**.

![Microsoft account picker for the administrative registration sign-in]({{ '/assets/images/endpoint-management/autopilot-work-school-signin.png' | relative_url }})

![Microsoft Graph permission prompt used during the online upload]({{ '/assets/images/endpoint-management/autopilot-graph-permission-consent.png' | relative_url }})

![Windows sign-in prompt with No, this app only selected]({{ '/assets/images/endpoint-management/autopilot-no-this-app-only.png' | relative_url }})

These choices keep the hardware-registration session separate from the employee's later OOBE sign-in. **Do not sign the administrator in as the Windows device's intended user.**

### 6. Confirm That the Upload Succeeded

After authentication completes, check the PowerShell output. The successful lab run reported **one device imported** and completed Autopilot synchronisation.

![Successful online Windows Autopilot import and synchronisation]({{ '/assets/images/endpoint-management/autopilot-online-import-success.png' | relative_url }})

Now verify the device in the [Microsoft Intune admin center](https://intune.microsoft.com/):

**Devices → Device onboarding → Enrollment → Windows Autopilot → Devices**

1. Select **Sync** or **Refresh**, if available, and locate the new VirtualBox record.
2. Open it and check the hardware registration details.
3. Confirm the following fields:

| Field | Expected Wire Finance value |
|---|---|
| Manufacturer | `innotek GmbH` |
| Model | `VirtualBox` |
| Group Tag | `CORPORATE` |
| Enrollment state | **Not enrolled** at this stage |

Immediately after registration, the profile status may show **Not assigned** while group membership and assignment evaluation complete. Do not begin the employee setup yet.

![Registered VirtualBox device with the CORPORATE Group Tag and profile initially Not assigned]({{ '/assets/images/endpoint-management/autopilot-device-initially-unassigned.png' | relative_url }})

### 7. Verify Dynamic Group Membership

The `CORPORATE` tag should cause the device to enter the existing group `SG-DEVICES-AUTOPILOT-CORPORATE`. Its dynamic membership rule is:

```text
(device.devicePhysicalIds -any (_ -eq "[OrderID]:CORPORATE"))
```

Open the [Microsoft Entra admin center](https://entra.microsoft.com/) and navigate to:

**Entra ID → Groups → All groups → SG-DEVICES-AUTOPILOT-CORPORATE → Members**

Look for the newly registered VirtualBox device object. The new VirtualBox device appeared as a member in the lab.

![Registered VirtualBox device in the corporate Autopilot dynamic group]({{ '/assets/images/endpoint-management/autopilot-corporate-group-membership.png' | relative_url }})

A device appearing here **doesn't mean Windows has completed Microsoft Entra join**. This is the Autopilot-associated device object used for pre-deployment targeting.

### 8. Confirm That the Deployment Profile Is Assigned

Return to:

**Intune → Devices → Device onboarding → Enrollment → Windows Autopilot → Devices**

Refresh the device list, open the VirtualBox record and confirm:

| Field | Verified result |
|---|---|
| Group tag | `CORPORATE` |
| Profile status | **Assigned** |
| Assigned profile | `WF_AP_CORPORATE_USER_DRIVEN` |
| User | **Unassigned** |
| Enrollment state | **Not enrolled** |
| Associated Intune device | **N/A** |

![Corporate Autopilot device record with assigned deployment profile]({{ '/assets/images/endpoint-management/autopilot-device-profile-assigned.png' | relative_url }})

**This is the pass condition for Lesson 7.** The policy being assigned to a group is not sufficient proof on its own; the **individual device record must show `Assigned` and the correct profile name**.

If it remains **Not assigned**, allow time for group evaluation, confirm the Group Tag and included-group assignment, then sync or refresh and check again. See the troubleshooting section below if it does not resolve.

### 9. Restart Windows OOBE

After confirming the profile assignment, return to the Command Prompt inside the VM and run:

```cmd
shutdown /r /t 0
```

This requests an immediate Windows restart, allowing OOBE to contact the Autopilot service again. Make sure the import has completed before restarting.

**Stop at OOBE after the restart.** The branded onboarding screens, Olivia's first work sign-in, Microsoft Authenticator registration, Enrollment Status Page and actual Intune enrollment belong in **Lesson 8**. Do not proceed with her credentials in this lesson.

### Common Errors and Solutions (Reference)

These are reference fixes for problems that can arise during registration. **Follow the main procedure first; use this section only if the relevant error appears.**

| Symptom | Likely cause | What to do |
|---|---|---|
| `Install-Script` can't find `Get-WindowsAutopilotInfo` | PowerShell Gallery isn't registered or reachable | Run `Get-PSRepository`; register PSGallery if absent, then retry `Find-Script` and `Install-Script` |
| `Get-WindowsAutopilotInfo.ps1` is not recognised | The Scripts folder isn't in the command search path | Run the installed `.ps1` using its full path |
| Script execution is blocked in a new PowerShell session | The earlier execution-policy setting was process-scoped | Run `Set-ExecutionPolicy -Scope Process -ExecutionPolicy RemoteSigned` again in the new session |
| `AADSTS70008` appears during Microsoft sign-in | The authentication code or token expired | Start a fresh interactive work/school sign-in and rerun the registration command |
| Autopilot record remains **Not assigned** | Dynamic membership or profile assignment hasn't completed, or the tag/target is incorrect | Verify `CORPORATE`, the dynamic group, and profile assignment; sync and refresh |
| OOBE doesn't load the corporate profile after a verified assignment | OOBE may not yet have refreshed its Autopilot registration state | Restart once after assignment, verify connectivity, then follow Microsoft's reset guidance if required |

#### PowerShell Gallery or script discovery failure

If `Get-PSRepository` returns no usable repository, run the following commands before attempting registration again:

```powershell
Register-PSRepository -Default -Verbose
Get-PSRepository
Find-Script -Name Get-WindowsAutopilotInfo
Install-Script -Name Get-WindowsAutopilotInfo -Force
```

![PowerShell failed to locate Get-WindowsAutopilotInfo before repository repair]({{ '/assets/images/endpoint-management/autopilot-install-script-error.png' | relative_url }})

#### Script installed, but PowerShell cannot run it by name

Use the full path rather than repeating the unsuccessful command:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy RemoteSigned
& "C:\Program Files\WindowsPowerShell\Scripts\Get-WindowsAutopilotInfo.ps1" -GroupTag CORPORATE -Online
```

![PowerShell script name not recognised before using its full path]({{ '/assets/images/endpoint-management/autopilot-script-path-error.png' | relative_url }})

#### Authentication code or token expired

If you see **AADSTS70008**, abandon the expired authentication window, check the VM's network connectivity and clock if necessary, and start a **new** work/school authentication session by rerunning the same online registration command.

![AADSTS70008 token-expiry message]({{ '/assets/images/endpoint-management/autopilot-expired-auth-token.png' | relative_url }})

### Registration Checkpoint

| Verification | Outcome |
|---|---|
| Windows PowerShell launched from OOBE | **Completed** |
| `Get-WindowsAutopilotInfo` installed and executed | **Completed** |
| Online device import and sync | **Successful** |
| Group Tag `CORPORATE` | **Verified** |
| Membership of `SG-DEVICES-AUTOPILOT-CORPORATE` | **Verified** |
| Device-specific assignment of `WF_AP_CORPORATE_USER_DRIVEN` | **Verified** |
| Microsoft Entra join / Intune enrollment | **Not yet — Lesson 8** |
| Olivia's first work-account sign-in | **Not yet — Lesson 8** |

Our first virtual endpoint is now known to Windows Autopilot and has the correct provisioning profile. The environment is prepared for its user-driven deployment without prematurely signing in or treating Autopilot registration as completed enrollment.

**Next: First Windows Autopilot Deployment — Olivia Carter (Lesson 8).** We'll work through the employee experience, Microsoft Authenticator registration, ESP and initial managed desktop.

---

**Microsoft references:** [Manually register Windows Autopilot devices](https://learn.microsoft.com/en-us/autopilot/add-devices) · [Windows Autopilot user-driven mode](https://learn.microsoft.com/en-us/autopilot/user-driven) · [Create device groups for Windows Autopilot](https://learn.microsoft.com/en-us/autopilot/enrollment-autopilot)
