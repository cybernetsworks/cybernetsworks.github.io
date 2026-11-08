---
layout: lesson
title: "BitLocker Encryption and Recovery-Key Protection"
series: wire-finance
parent: endpoint-security-and-compliance-implementation
order: 1
description: "Configure and verify Microsoft Intune BitLocker protection for the first Wire Finance Windows endpoint, including TPM startup, Entra recovery-key escrow, and the Event 853 virtual optical-drive troubleshooting case."
---

Our first Wire Finance device has successfully completed Windows Autopilot enrollment. Before expanding the pilot, we need to protect the information stored on that endpoint—not just confirm it appears in Intune.

This lesson configures **BitLocker through Microsoft Intune**, verifies that protection is actually active on `DESKTOP-QDSCPUK`, and confirms that an authorised administrator can find its recovery-password record in Microsoft Entra ID.

> **Lab outcome:** `WF-SEC-WIN-BITLOCKER` was applied successfully to Olivia Carter's Windows 11 VirtualBox device. BitLocker finished with **Protection On**, **TPM** and **Numerical Password** protectors, and a matching recovery-key identifier in Intune. The VM required one VirtualBox-specific correction, documented separately under **Troubleshooting**.

### 1. Identify the Pilot Scope

Wire Finance already uses separate groups for provisioning and security-policy testing:

| Object | Role |
|---|---|
| `SG-DEVICES-AUTOPILOT-CORPORATE` | Dynamic group for devices tagged `CORPORATE`; targets the Autopilot experience |
| `SG-DEVICES-WINDOWS-PILOT` | **Assigned membership**; targets security changes to approved pilot devices |
| `WF-LT-001` | VirtualBox VM / asset reference |
| `DESKTOP-QDSCPUK` | The Windows hostname of Olivia's enrolled endpoint |

Before assigning any disk-encryption settings, open **Microsoft Entra admin center → Entra ID → Groups → All groups → `SG-DEVICES-WINDOWS-PILOT` → Members**. Add the **correct Microsoft Entra device object** for `DESKTOP-QDSCPUK` if it isn't present. Match its Entra Device ID to the enrolled Intune device when necessary.

**Why was manual membership needed?** Unlike the Autopilot corporate group, `SG-DEVICES-WINDOWS-PILOT` was created with **Assigned** membership. It won't automatically populate when another device completes enrollment. This keeps our first security rollout deliberately small.

### 2. Check the Endpoint Before Changing Encryption

On Olivia's VM, open **Windows PowerShell as Administrator** and run:

```powershell
Get-Tpm
Confirm-SecureBootUEFI
Get-BitLockerVolume -MountPoint 'C:'
reagentc /info
```

The results recorded for `DESKTOP-QDSCPUK` were:

| Check | Verified result |
|---|---|
| TPM | Present, enabled, activated, owned and ready |
| Secure Boot | `True` |
| Windows Recovery Environment | `Enabled` |
| OS volume | `C:` — approximately 78.92 GB |
| Existing encryption | 100% **Used Space Only Encrypted**, XTS-AES 128 |
| Existing BitLocker protection | **Off**, with **no key protectors** |

![PowerShell Get-Tpm showing the virtual TPM enabled and ready]({{ '/assets/images/endpoint-security/wf-bitlocker-tpm-ready.png' | relative_url }})

*`Get-Tpm` confirms that the VM's TPM is present and ready.*

![Secure Boot returns True]({{ '/assets/images/endpoint-security/wf-bitlocker-secure-boot.png' | relative_url }})

*Secure Boot is enabled, allowing TPM-backed BitLocker startup protection.*

![Windows Recovery Environment status enabled]({{ '/assets/images/endpoint-security/wf-bitlocker-winre-enabled.png' | relative_url }})

*WinRE is enabled before silent BitLocker activation.*

**Important:** A drive can be 100% encrypted while **Protection Status** is still **Off**. On this VM, Windows had already prepared encryption without creating the final TPM and recovery-password protectors. We therefore needed to **activate protection**, not force a new encryption algorithm.

Before encryption-policy testing, preserve a **recoverable powered-off copy of the VirtualBox VM**, including its virtual disk, configuration, EFI/NVRAM state, virtual TPM state and any snapshot dependencies. Store the copy securely, and do not start an identity-duplicate backup alongside the original device. Confirm the original VM has no unsupported third-party disk-encryption software.

### 3. Create the Intune BitLocker Policy

Open the [Microsoft Intune admin center](https://intune.microsoft.com/) and go to:

**Endpoint security → Disk encryption → Create Policy**

Set:

| Field | Wire Finance value |
|---|---|
| Platform | **Windows** |
| Profile | **BitLocker** |
| Name | `WF-SEC-WIN-BITLOCKER` |
| Description | Wire Finance corporate BitLocker encryption and recovery protection — pilot deployment |

### BitLocker settings

Configure the following values on **Configuration settings**:

| Setting | Value |
|---|---|
| Require Device Encryption | **Enabled** |
| Allow Warning For Other Disk Encryption | **Disabled** |
| Allow Standard User Encryption | **Enabled** |
| Configure Recovery Password Rotation | **Refresh on for Entra ID-joined devices** |
| Provide the unique identifiers for your organisation | **Not configured** |
| Choose drive encryption method and cipher strength | **Not configured** |

We deliberately left the encryption algorithm unconfigured. `C:` already used **XTS-AES 128**, and changing an existing volume's cipher is not the objective of this deployment. Suppressing other-encryption warnings was appropriate only after checking that the VM had no competing encryption product.

#### Operating System Drives

| Setting | Wire Finance value |
|---|---|
| Enforce drive encryption type on operating system drives | **Enabled** |
| Select encryption type | **Used Space Only encryption** |
| Require additional authentication at startup | **Enabled** |
| Configure TPM startup | **Require TPM** |
| Allow BitLocker without a compatible TPM | **False** |
| Configure TPM startup PIN | **Do not allow startup PIN with TPM** |
| Configure TPM startup key | **Do not allow startup key with TPM** |
| Configure TPM startup key and PIN | **Do not allow startup key and PIN with TPM** |
| Choose how BitLocker-protected operating system drives can be recovered | **Enabled** |
| Configure user storage of BitLocker recovery information | **Require 48-digit recovery password** |
| Allow 256-bit recovery key (`OSRecoveryKeyUsageDropDown_Name`) | **Do not allow** |
| Allow data recovery agent | **False** |
| Configure storage of recovery information to AD DS | **Store recovery passwords and key packages** |
| Save recovery information to AD DS for OS drives | **True** |
| Do not enable BitLocker until recovery information is stored to AD DS for OS drives | **True** |
| Omit recovery options from the setup wizard | **True** |
| Configure pre-boot recovery message and URL | **Not configured** |

**Why does Intune mention AD DS?** These are legacy-labelled operating-system recovery controls. For our **cloud-only Microsoft Entra joined** device, the recovery password was actually backed up to **Microsoft Entra ID**, which we separately verified. Storing *key packages* in Microsoft Entra isn't established by this result; the verified record is the recovery password.

The startup choices give Olivia a normal **TPM-only boot**, without requiring an interactive pre-boot PIN or USB startup key. They don't remove the need for a separate Windows account sign-in.

Leave the **Fixed Data Drives** and **Removable Data Drives** sections **Not configured** for this initial OS-volume pilot. Other drive types can receive their own approved policies later.

![Reviewed BitLocker OS drive and recovery settings in Intune]({{ '/assets/images/endpoint-security/wf-bitlocker-policy-settings.png' | relative_url }})

### 4. Assign the Policy Only to Our Pilot

On **Scope tags**, use **Default**, consistent with the Intune RBAC assignment already established for Wire Finance.

On **Assignments**, include **`SG-DEVICES-WINDOWS-PILOT`** and add **no exclusions**. Verify that the group contains only the approved devices for this trial; for our initial BitLocker test, the intended target is Olivia's VM.

![Default scope tag and the Windows pilot group selected as included assignment]({{ '/assets/images/endpoint-security/wf-bitlocker-pilot-targeting.png' | relative_url }})

Select **Review + create**, verify the settings, and create the policy. Do **not** target `SG-DEVICES-AUTOPILOT-CORPORATE` or all managed devices simply because Autopilot provisioning was successful.

### 5. Verify Deployment in Intune

Open **Endpoint security → Disk encryption → `WF-SEC-WIN-BITLOCKER`**. Review **Device and user check-in status**.

If **Device assignment status** appears empty, open it and select **Generate report**; the report might not have been generated yet. Wait for the result and check the row for `DESKTOP-QDSCPUK` rather than repeatedly recreating the policy.

Our completed report showed **one device — Success**, with **zero errors, conflicts or pending assignments**.

![BitLocker device assignment report showing one successful pilot device]({{ '/assets/images/endpoint-security/wf-bitlocker-intune-success.png' | relative_url }})

**A successful policy report is not the final security check.** It confirms the device accepted the policy/reporting state, not that encryption protection is active or the recovery key is safely retrievable.

### 6. Verify Protection and Recovery-Key Escrow

On the VM, run in elevated PowerShell:

```powershell
manage-bde -status C:

# Show protector types without printing the secret recovery password.
(Get-BitLockerVolume -MountPoint 'C:').KeyProtector |
    Select-Object KeyProtectorType
```

The final recorded values were:

```text
Conversion Status:    Used Space Only Encrypted
Percentage Encrypted: 100.0%
Encryption Method:    XTS-AES 128
Protection Status:    Protection On
Key Protectors:      Numerical Password
                     TPM
```

![Final BitLocker status showing Protection On and TPM plus Numerical Password protectors]({{ '/assets/images/endpoint-security/wf-bitlocker-protection-on.png' | relative_url }})

For the **recovery-key checkpoint**:

1. In Intune, open **Devices → All devices → `DESKTOP-QDSCPUK` → BitLocker recovery keys**.
2. Confirm that a recovery-password record exists for this device.
3. Obtain the **recovery protector ID** from PowerShell, without revealing the 48-digit password:

   ```powershell
   Get-BitLockerVolume -MountPoint 'C:' |
       Select-Object -ExpandProperty KeyProtector |
       Where-Object KeyProtectorType -eq 'RecoveryPassword' |
       Select-Object KeyProtectorType, KeyProtectorId
   ```

4. Compare that identifier with the record in Intune.

For Wire Finance, the identifier **matched**. PowerShell rendered letters in uppercase while Intune displayed lowercase; the casing difference does not change the identifier.

**Do not publish the 48-digit recovery password, recovery-key export, or sensitive device identifiers.** The key was confirmed in the portal, but the secret itself is intentionally not reproduced in this lesson.

### Troubleshooting — Silent Encryption Blocked by a Mounted ISO

Follow the normal procedure above first. This section records the real blocker found on our VirtualBox pilot and its resolution.

#### Symptom

The Intune policy assignment showed **Success**, but `manage-bde -status C:` continued to report **Protection Off** and **Key Protectors: None Found**.

![BitLocker volume encrypted but protection off before resolving the issue]({{ '/assets/images/endpoint-security/wf-bitlocker-before-protection.png' | relative_url }})

#### Find the cause

Open **Event Viewer → Applications and Services Logs → Microsoft → Windows → BitLocker-API → Management**. Review current Error entries and open the **General** tab.

Our VM repeatedly logged **Event ID 853**:

```text
Failed to enable Silent Encryption. TPM is not available.
Error: BitLocker Drive Encryption detected bootable media (CD or DVD).
```

![Event 853 showing that bootable optical media prevented silent BitLocker activation]({{ '/assets/images/endpoint-security/wf-bitlocker-event-853.png' | relative_url }})

The earlier `Get-Tpm` result showed a ready TPM, so the event's **bootable-media detail** was the decisive clue. The Windows installation ISO was **still attached to the VM's virtual optical drive**.

#### Resolve the blocker

1. In the running VirtualBox VM, open **Devices → Optical Drives** and select **Remove disk from virtual drive** to unmount the Windows installation ISO.
2. Preserve a **powered-off, recoverable VM backup** before testing the next boot. Retain the virtual TPM and EFI-related machine state; copying just the VDI is insufficient for a faithful machine recovery point.
3. Start Windows normally with the optical media detached, leaving the virtual TPM, Secure Boot and virtual disk configuration unchanged.
4. Recheck `manage-bde -status C:` and the protector types. If protection remains Off, inspect **new** BitLocker-API events instead of repeatedly rebooting, decrypting, clearing TPM or changing policies at random.
5. Verify the matching recovery-key record in Microsoft Entra ID before calling the control complete.

**Observed result:** After the installation ISO was removed and Windows started again, BitLocker showed **Protection On**, a **TPM** protector and a **Numerical Password** protector. The recovery-key identifier subsequently matched the Intune recovery record.

Microsoft documents this exact Event 853 bootable-media condition and the **remove media → restart → verify protection** remedy.

### Final Verification Checkpoint

| Requirement | Recorded Wire Finance result |
|---|---|
| Target device in `SG-DEVICES-WINDOWS-PILOT` | Verified — explicit assigned membership |
| `WF-SEC-WIN-BITLOCKER` policy | Created and scoped to pilot |
| Intune device assignment | **Success (1 device)** |
| TPM / Secure Boot / WinRE | Ready / True / Enabled |
| Windows volume encryption | **100%**, XTS-AES 128 |
| Actual BitLocker protection | **On** |
| Key protectors | **TPM + Numerical Password** |
| Recovery password in Microsoft Entra ID | **Confirmed; identifier matched** |
| Other endpoint security controls | **Not yet part of this milestone** |

**Milestone: `WF-SEC-PILOT-BITLOCKER-001` — BitLocker protection and recovery escrow verified.**

We now have our first Windows endpoint-security control deployed, tested and documented. The same pilot group will be used cautiously for the next modular policy, without broadening the deployment until we've tested and documented each control.

**Next: Microsoft Defender Antivirus — `WF-SEC-WIN-ANTIVIRUS`.**

---

**Microsoft references:** [Configure BitLocker settings](https://learn.microsoft.com/en-us/windows/security/operating-system-security/data-protection/bitlocker/configure) · [BitLocker enforcement issues with Intune (including Event 853)](https://learn.microsoft.com/en-us/troubleshoot/windows-client/windows-security/enforcing-bitlocker-policies-by-using-intune-known-issues) · [BitLocker recovery process](https://learn.microsoft.com/en-us/windows/security/operating-system-security/data-protection/bitlocker/recovery-process)
