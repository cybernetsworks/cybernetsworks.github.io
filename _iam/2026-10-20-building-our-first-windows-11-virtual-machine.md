---
layout: lesson
title: "Building Our First Windows 11 Virtual Machine"
series: wire-finance
parent: endpoint-device-management-implementation
order: 1
description: "Building the first Wire Finance Windows 11 Pro virtual workstation in Oracle VirtualBox and preparing it for Windows Autopilot."
---

We have designed our corporate device provisioning model.

Now it is time to build an actual workstation.

Our first pilot employee is **Olivia Carter**, a Finance Analyst whose identity, group memberships, and licensing have already been configured in Microsoft Entra ID.

But Olivia doesn't have a workstation yet.

We need to create one.

For our first deployment, we used a Windows 11 virtual machine running locally in Oracle VirtualBox.

This became our first test endpoint for Windows Autopilot and Microsoft Intune.

### Why a Virtual Machine?

In a real organisation, employees would typically receive physical corporate laptops.

However, Wire Finance is a lab environment, and purchasing multiple physical devices would introduce unnecessary cost and complexity at this stage.

We initially considered using Microsoft Azure virtual machines.

Instead, I chose to use **Oracle VirtualBox** on my local computer.

This gave us a practical environment for preparing Windows installations, testing Autopilot, and troubleshooting deployment issues without the ongoing cost of Azure compute.

Our pilot machine represents the corporate asset:

`WF-LT-001`

| Item | Pilot Configuration |
|---|---|
| Employee | Olivia Carter |
| Department | Finance |
| Device asset identifier | WF-LT-001 |
| Virtualisation platform | Oracle VirtualBox |
| Operating system | Windows 11 Pro |
| Intended ownership | Corporate |
| Device provisioning method | Windows Autopilot |
| Device identity model | Microsoft Entra Joined |
| Management platform | Microsoft Intune |

One important distinction: `WF-LT-001` is our intended asset identifier and the name used for the VirtualBox machine.

It does not automatically mean Windows itself will receive that hostname.

We will examine the actual Windows device name later during the Autopilot deployment.

### Step 1 — Prepare the Virtualisation Environment

Before creating the VM, we need the following:

- Oracle VirtualBox installed on the host computer.
- A Windows 11 installation ISO.
- A host computer capable of running Windows 11 in a virtual machine.
- Sufficient memory, processor, and storage resources.
- Internet connectivity for corporate enrollment.
- Appropriate Windows licensing.

#### Download Oracle VirtualBox

Oracle VirtualBox is a desktop virtualisation platform that allows us to create and run Windows virtual machines locally.

Download it from the official website:

**[Download Oracle VirtualBox](https://www.virtualbox.org/wiki/Downloads)**

Select the installation package appropriate for your host operating system and complete the installation.

### Download the Windows 11 ISO

Next, we need the Windows 11 installation image.

Microsoft provides an official ISO download page:

**[Download Windows 11 ISO — Microsoft](https://www.microsoft.com/software-download/windows11)**

On the Microsoft download page:

1. Scroll to **Download Windows 11 Disk Image (ISO) for x64 devices**.
2. Select the available Windows 11 ISO edition from the dropdown menu.
3. Click **Confirm**.
4. Choose the required product language. For this walkthrough, we used English (United States).
5. Confirm the language selection.
6. Select **64-bit Download**.
7. Save the ISO file somewhere accessible on your host computer.

The ISO will later be attached to the VirtualBox machine as a virtual optical disc.

Microsoft may publish newer versions of Windows 11 over time, so the filename and build number offered to readers may differ from those used during our original implementation.

### ISO Used in the Wire Finance Lab

Our original installation media was recorded as:

```text
Windows11_Client_x64_en-us_26300_9457.iso
```

The VirtualBox configuration summary showed this ISO with a size of approximately **8.43 GB**.

This is the filename of the installation media used in our lab, not a guarantee that Microsoft currently offers that exact build on the public download page.

Readers should use an appropriately licensed, supported Windows 11 image and confirm its compatibility with the intended Autopilot testing environment.

## Step 2 — Create the Virtual Machine

Open Oracle VirtualBox and select **New**.

This launches the virtual machine creation wizard.

For our pilot, we created the VM with the following identity:

| Setting | Value |
|---|---|
| Virtual machine name | `WF-LT-001` |
| Operating system type | `Windows 11 (64-bit)` |
| Installation media | Windows 11 x64 ISO |
| Intended Windows edition | Windows 11 Pro |

Attach the downloaded Windows 11 ISO when prompted.

### Skip Unattended Installation

One important decision during this build was to **skip VirtualBox unattended installation**.

VirtualBox can automate Windows installation, including parts of account setup and other configuration choices.

However, our objective was not simply to produce a Windows desktop.

We wanted to preserve the native Windows Out-of-Box Experience (OOBE) for later testing with Microsoft Autopilot.

This would allow us to investigate the actual onboarding experience involving:

- Organisational identity authentication
- Windows Autopilot
- Microsoft Entra Join
- Microsoft Intune enrollment
- Enrollment Status Page
- Employee application delivery

Skipping unattended installation helped ensure that the standard Windows setup flow remained available for our pilot.

## Step 3 — Configure the Virtual Hardware

After creating the VM, I reviewed its configuration in VirtualBox.

The screenshot below shows the actual configuration used for our pilot workstation.

![WF-LT-001 VirtualBox configuration summary]({{ '/assets/images/endpoint-management/vm-configuration-summary.png' | relative_url }})

*Figure 1: Original VirtualBox configuration summary for WF-LT-001.*

### Confirmed VirtualBox Configuration

| Configuration Area | Implemented Value |
|---|---|
| VM name | `WF-LT-001` |
| Operating system | Windows 11 (64-bit) |
| Base memory | 6725 MB |
| Processors | 3 |
| Boot order | Floppy, Optical, Hard Disk |
| TPM | v2.0 |
| EFI | Enabled |
| Secure Boot | Enabled |
| Acceleration | Nested Paging, Hyper-V Paravirtualization |
| Video memory | 128 MB |
| Graphics controller | VBoxSVGA |
| Virtual disk | `WF-LT-001.vdi` |
| Virtual disk capacity | 80.00 GB |
| Storage controller | SATA |
| Network adapter 1 | Intel PRO/1000 MT Desktop (NAT) |
| Network adapter 2 | Intel PRO/1000 MT Desktop (Bridged Adapter) |
| USB controller | xHCI |
| Shared folders | None |

The bridged adapter was associated with the host's Intel Dual Band Wireless-AC 8265 interface.

These are the settings recorded in our actual VM configuration, rather than generic recommended values.

### Understanding the Important Settings

**Memory and processors**

We allocated 6725 MB of RAM and three virtual processors.

This provided resources for Windows 11 installation and subsequent testing.

**TPM, EFI, and Secure Boot**

We configured TPM 2.0 and enabled EFI and Secure Boot.

These settings supported the Windows 11 installation requirements.

**Storage**

The workstation used an 80 GB virtual disk connected through the SATA controller.

This provided storage for Windows 11 and the applications that would later be deployed.

**Networking**

Two network adapters were configured:

- Adapter 1 used NAT.
- Adapter 2 used Bridged Adapter mode.

This was the configuration present in our lab. It should not be interpreted as a requirement to use two adapters for every Autopilot deployment.

The important requirement for later registration is that the Windows machine can reach the necessary Microsoft services.

### Keep the Virtual Hardware Stable

A further consideration becomes important when we introduce Windows Autopilot.

The device will be registered using hardware-related identification information.

After capturing and registering that identity, we need to avoid unnecessary changes that could alter how the device is identified.

For this reason, we kept the virtual hardware stable after completing the Autopilot registration process.

We'll explain the hardware identification process in the dedicated registration walkthrough.

## Step 4 — Install Windows 11

With the VM configured and the installation ISO attached, start the machine.

Windows Setup should launch from the virtual optical drive.

During our original build, the installer reached the language selection screen shown below.

![Windows 11 installer language selection in VirtualBox]({{ '/assets/images/endpoint-management/windows-11-setup-language.png' | relative_url }})

*Figure 2: Windows 11 installation running inside the WF-LT-001 virtual machine.*

The observed installation settings were:

| Installation Setting | Selected Value |
|---|---|
| Language to install | English (United States) |
| Time and currency format | English (United States) |

These are Windows installation settings.

They are separate from the United Kingdom identity attributes previously configured for our workforce users in Microsoft Entra ID.

Proceed through Windows Setup.

For readers reproducing the lab:

1. Confirm the installation language and regional preferences.
2. Continue to the Windows installation process.
3. Select Windows 11 Pro where an edition selection is presented and licensing permits.
4. Accept the applicable licence terms.
5. Select the appropriate installation option for a clean VM.
6. Choose the virtual hard disk as the Windows installation destination.
7. Allow Windows to install and restart as required.

The exact prompts may vary depending on the Windows 11 image and installation version.

During this stage, we are installing the operating system only.

We are not yet joining the VM to Microsoft Entra ID or enrolling it into Intune.

## Step 5 — Reach the Windows Out-of-Box Experience

After Windows installation completed, the VM reached the initial Windows setup experience.

This is called the **Out-of-Box Experience (OOBE)**.

It's the process a new employee would typically encounter when powering on a newly installed Windows workstation.

Our machine reached the country or region selection screen.

![Windows 11 initial OOBE region selection]({{ '/assets/images/endpoint-management/windows-11-oobe-region.png' | relative_url }})

*Figure 3: WF-LT-001 at the Windows 11 OOBE region selection screen.*

This confirmed that Windows had installed and progressed to the initial user setup stage.

However, we were not going to complete the normal personal workstation setup.

The VM was intended to become a Wire Finance corporate endpoint.

That meant the next activity was to prepare it for organisational provisioning.

At this point in our walkthrough:

- Windows 11 was installed.
- The VM reached OOBE.
- The device had not yet been registered with Windows Autopilot.
- Olivia had not yet completed her organisational sign-in.
- Microsoft Entra Join and Intune enrollment had not yet taken place.

We needed to complete the appropriate corporate provisioning configuration before proceeding with employee onboarding.

## Step 6 — Preserve the VM for Autopilot

Rather than completing Windows setup with an ordinary personal account, we retained the OOBE experience for corporate provisioning.

This was deliberate.

Our device provisioning model requires a corporate workstation to be registered, classified, assigned the appropriate deployment configuration, and then enrolled through the approved organisational process.

We also deferred **VirtualBox Guest Additions** until after successful enrollment.

Guest Additions can improve how a virtual machine interacts with its host, but installing them was not necessary for our initial provisioning objective.

By keeping the environment focused on the Windows installation and corporate onboarding process, we reduced unnecessary variables during troubleshooting.

## What Have We Built?

We now have our first Windows 11 virtual workstation prepared for the next phase.

| Validation Point | Result |
|---|---|
| Virtualisation platform | Oracle VirtualBox |
| VM identity | WF-LT-001 |
| Operating system | Windows 11 Pro |
| Base memory | 6725 MB |
| Virtual processors | 3 |
| Virtual disk | 80 GB |
| TPM 2.0 | Enabled |
| EFI | Enabled |
| Secure Boot | Enabled |
| Windows installation | Completed |
| Windows OOBE | Reached |
| Unattended installation | Skipped |
| Guest Additions | Deferred |
| Autopilot registration | Next implementation stage |
| Olivia's organisational sign-in | To be covered later |

This is our first practical endpoint milestone.

But it is important not to confuse having Windows installed with having a fully managed corporate workstation.

**Installing Windows gives us the operating system. Our next steps will establish the organisational identity and management relationship.**

## Next — Preparing Wire Finance for Managed Device Enrollment

With WF-LT-001 prepared, the next step is to configure the Microsoft environment that will manage our endpoints.

We'll establish the automatic MDM enrollment configuration and confirm how Microsoft Entra Join and Intune management fit into the provisioning process.

After that, we'll register our first virtual machine with Windows Autopilot.

And that part of the journey deserves careful documentation.

Our first registration involved troubleshooting PowerShell execution restrictions, script installation and location, Microsoft Graph authentication, permission prompts, and an expired authentication request.

Rather than skipping directly to the successful command, we'll document how those issues occurred and how we resolved them.

**Next: Automatic MDM Enrollment and Microsoft Entra Join — preparing Wire Finance for managed Windows device provisioning.**