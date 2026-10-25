---
layout: lesson
title: "Building Our First Windows 11 Virtual Machine"
series: wire-finance
parent: endpoint-device-management-implementation
order: 6
description: "Create our first Wire Finance Windows 11 Pro virtual machine in Oracle VirtualBox, document the exact hardware settings, and stop at Windows OOBE ready for Autopilot registration."
---

We've put the cloud-side foundations in place.

Our Intune administrator role, automatic enrollment settings, Enrollment Status Page, Autopilot deployment profile, and required application assignments are configured. We even returned to `WF-ESP-CORPORATE` to make **Company Portal our only selected ESP-blocking app**.

Now it's time to give those configurations a device to work with.

Instead of buying a physical laptop or running an Azure VM, I decided to use **Oracle VirtualBox** to build our first Windows 11 workstation locally. This gives us a repeatable lab without adding Azure compute costs.

**Our target for this lesson:** create `WF-LT-001`, install Windows 11 Pro manually, and stop at the first Windows **Out-of-Box Experience (OOBE)** screen. We won't register the hardware or sign Olivia in just yet.

### 1. Meet Our First Pilot Workstation

We're treating the virtual machine as a standard corporate laptop assigned to **Olivia Carter, Finance Analyst**.

| Item | Our decision |
|---|---|
| Asset identifier | `WF-LT-001` |
| VirtualBox VM name | `WF-LT-001` |
| Virtualization platform | Oracle VirtualBox on the local computer |
| Guest OS | Windows 11 Pro, 64-bit |
| Intended employee | Olivia Carter — Finance Analyst |
| Intended deployment | User-driven Windows Autopilot, Microsoft Entra joined |
| Intended management | Microsoft Intune |

One naming distinction matters: **`WF-LT-001` is our VM name and asset identifier.** It doesn't automatically become the Windows computer hostname. We deliberately left **Apply device name template = No** in our Autopilot profile, so we'll inspect the actual Windows hostname after deployment.

### 2. Prepare VirtualBox and the Windows 11 ISO

I downloaded the software from the official sources:

- [Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads) for the computer hosting the VM.
- [Microsoft Windows 11](https://www.microsoft.com/en-gb/software-download/windows11) for the Windows 11 x64 ISO.

After installing VirtualBox, I opened **VirtualBox Manager** and selected **New**.

For the initial machine definition, I used:

| Option | Selection |
|---|---|
| Name | `WF-LT-001` |
| Operating system | Microsoft Windows |
| Version | Windows 11 (64-bit) |
| ISO image | The Windows 11 x64 ISO |
| Unattended installation | **Skipped** |

**Skipping unattended installation was intentional.** VirtualBox can create a user account and complete parts of Windows setup automatically. That's convenient for ordinary testing, but not for our lab: we need to see the original OOBE so we can perform Autopilot registration before Olivia's first organizational sign-in.

Depending on the VirtualBox version, the option might appear as **Skip Unattended Installation** or **Install OS Using Unattended Installation**. I made sure the unattended installation was disabled.

### 3. Configure the Virtual Hardware

These are the resource values I used when creating `WF-LT-001`:

| VirtualBox setting | Value used |
|---|---|
| Base memory | **6,725 MB** |
| Processors | **3** |
| Virtual hard disk | **80 GB**, `WF-LT-001.vdi` |
| TPM type | **v2.0** |
| EFI | **Enabled** |
| Secure Boot | **Enabled** |
| Graphics controller | **VBoxSVGA** |
| Video memory | **128 MB** |
| Network Adapter 1 | **Intel PRO/1000 MT Desktop — NAT** |
| Network Adapter 2 | **Intel PRO/1000 MT Desktop — Bridged Adapter** |

![Actual VirtualBox configuration for the WF-LT-001 virtual machine]({{ '/assets/images/endpoint-management/wf-lt-001-virtualbox-configuration.png' | relative_url }})

Before powering it on, I checked the important items in the VM's **Settings**:

1. **System:** Three processors, EFI and Secure Boot enabled, with virtual **TPM 2.0** configured.
2. **Display:** `VBoxSVGA` graphics with **128 MB** video memory.
3. **Storage:** `WF-LT-001.vdi` attached on **SATA Port 0**, and the Windows 11 ISO mounted as an optical drive on **SATA Port 1**.
4. **Network:** Adapter 1 configured for **NAT**; Adapter 2 configured as a **Bridged Adapter** to the host wireless interface.

#### Why Two Network Adapters?

That's how I configured this VM, but **NAT alone is enough** for the first Wire Finance Autopilot lab. We primarily need outbound connectivity to Microsoft's services for script installation, Autopilot registration, Windows setup and Intune enrollment.

If you're following the project on your own machine, you can start with **Adapter 1 = NAT** and leave Adapter 2 disabled. The Bridged adapter was part of my lab configuration, **not an Autopilot requirement**. We will test connectivity when we register the device in Lesson 7.

I also kept the virtual hardware settings stable once the machine was ready. We'll capture its hardware identity in the next lesson, so changing virtual hardware afterwards could complicate device recognition.

#### Why TPM 2.0?

Windows 11 expects a compatible virtual TPM and UEFI/Secure Boot capability. That is separate from the **Autopilot deployment mode**: our **user-driven** deployment works with a VM and doesn't require TPM attestation. **Self-deploying** and **pre-provisioned** Autopilot modes rely on physical-device TPM attestation, so we aren't using those modes here.

### 4. Install Windows 11 Pro Manually

With the virtual hardware configured, I selected **`WF-LT-001` → Start** in VirtualBox Manager.

The VM booted from the mounted Windows 11 ISO and opened the normal Windows Setup screen.

![Windows 11 setup language selection inside the WF-LT-001 VM]({{ '/assets/images/endpoint-management/wf-lt-001-windows-setup-language.png' | relative_url }})

Here's the installation sequence I followed:

1. **Language and keyboard:** Select the installation language, regional format and keyboard options, then continue.
2. **Start installation:** Choose **Install Windows 11** or **Install now**, depending on the installer screen.
3. **Product key:** Supply an appropriate licence key if prompted. If Windows offers **I don't have a product key**, installation can continue, but valid activation rights still need to be addressed.
4. **Edition:** Choose **Windows 11 Pro**, not Home. If the ISO or product key selects the edition automatically, confirm it's Pro.
5. **Licence terms:** Read and accept the applicable terms.
6. **Installation type:** Choose the clean/custom installation option rather than upgrading an existing Windows installation.
7. **Disk:** Select the unallocated **80 GB virtual disk** and let Setup create the Windows partitions.
8. **Installation:** Allow Windows to copy files, install and restart as needed.

The exact wording may vary with the Windows 11 installer version. What matters is that we're installing **Windows 11 Pro cleanly without completing account setup**.

I also left **VirtualBox Guest Additions** for later. They weren't needed for the initial Windows installation or Autopilot registration, and I wanted to keep this first setup straightforward.

### 5. Stop at the Windows OOBE Region Screen

After Windows finished installing, it reached its **Out-of-Box Experience** and displayed the country/region prompt.

![First Windows OOBE region screen in WF-LT-001]({{ '/assets/images/endpoint-management/wf-lt-001-oobe-region.png' | relative_url }})

**This is exactly where I stopped.**

I didn't create a local user, sign in with a personal Microsoft account, or sign in as Olivia. We want her first work-account authentication—including Microsoft Authenticator setup—to happen during our **actual Autopilot deployment in Lesson 8**.

The next lesson will use this OOBE environment to open a command shell, retrieve the hardware identity and register the device in Windows Autopilot.

For now, the machine's journey looks like this:

```text
Microsoft environment and app assignments — READY
                     ↓
WF-LT-001 built in Oracle VirtualBox — DONE
                     ↓
Windows 11 Pro installed — DONE
                     ↓
Windows OOBE region screen — STOP HERE
                     ↓
Autopilot registration and Group Tag — LESSON 7
                     ↓
Verify device targeting and assigned profile — LESSON 7
                     ↓
Olivia's organizational sign-in — LESSON 8
```

### 6. Our VM-Build Checkpoint

| Check | Result |
|---|---|
| `WF-LT-001` created in VirtualBox | **Completed** |
| 6,725 MB RAM, 3 CPUs, 80 GB virtual disk | **Configured** |
| EFI, Secure Boot, TPM 2.0 | **Enabled** |
| NAT and Bridged adapters | **Configured** |
| Windows 11 Pro manual installation | **Completed** |
| Unattended installation | **Skipped** |
| First Windows OOBE region screen | **Reached** |
| Autopilot hardware registration | **Not yet in our walkthrough** |
| Entra join, Intune enrollment and first employee sign-in | **Not yet in our walkthrough** |

We now have an actual Windows machine to test against the cloud configurations prepared in Lessons 1–5. But creating the VM doesn't register it in Autopilot or make it an Intune-managed device. Those steps are still ahead.

**Next: Windows Autopilot Registration and Troubleshooting — Lesson 7.** We'll use `Shift + F10` at OOBE, work through the PowerShell repository and Microsoft Graph authentication problems we encountered, register the VM with Group Tag `CORPORATE`, and check that `WF_AP_CORPORATE_USER_DRIVEN` is assigned before proceeding to Olivia's first sign-in.

---

**Microsoft and Oracle references:** [Oracle VirtualBox: Create a VM and skip unattended installation](https://docs.oracle.com/en/virtualization/virtualbox/7.1/user/Introduction.html) · [Windows Autopilot scenarios on virtual machines](https://learn.microsoft.com/en-us/windows/deployment/windows-autopilot/demonstrate-deployment-on-vm) · [Windows Autopilot user-driven mode](https://learn.microsoft.com/en-us/autopilot/user-driven)