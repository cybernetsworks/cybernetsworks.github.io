---
layout: lesson
title: "Company Portal and Microsoft 365 Apps Deployment"
series: wire-finance
parent: endpoint-device-management-implementation
order: 5
description: "Create Wire Finance's required Company Portal and Microsoft 365 Apps deployments, target the correct pilot groups, and update the Enrollment Status Page to block on Company Portal only."
---

We've prepared our enrollment settings, created `WF-ESP-CORPORATE`, and assigned our user-driven Windows Autopilot profile.

Now we're going to answer a practical question: **when Olivia receives her new workstation, which applications should arrive automatically?**

For our first pilot, I chose two different application assignments. **Company Portal** is part of the device provisioning baseline; **Microsoft 365 Apps** follows our approved pilot users. We'll prepare both in Intune *before* building the Windows 11 VM.

### 1. Two Apps, Two Assignment Targets

| Application | Deployment type | Required assignment | ESP blocking? |
|---|---|---|---|
| **Company Portal** | Microsoft Store app (new), **System** install | `SG-DEVICES-AUTOPILOT-CORPORATE` (devices) | **Yes — selected blocking app** |
| **Microsoft 365 Apps - Wire Finance Pilot** | Microsoft 365 Apps (Windows 10 and later) | `SG-PILOT-IDENTITY` (users) | **No** |

This distinction is deliberate. The corporate device should receive Company Portal, while each approved pilot employee should receive the productivity apps needed for everyday work.

**Required** means Intune should deploy an app automatically to the assigned population. It is not the same as **Available**, where users choose an app from Company Portal. We haven't published any self-service *Available* apps in this first baseline.

### 2. Create Company Portal in Microsoft Intune

I opened the [Microsoft Intune admin center](https://intune.microsoft.com/) and navigated to:

**Apps → All apps → Create**

In **Select app type**, I chose **Microsoft Store app (new)** and selected **Select**. On **App information**, I used **Search the Microsoft Store app (new)**, searched for **Company Portal**, selected the Microsoft-published result, and confirmed the selection.

The key values were:

| Field | Value used |
|---|---|
| **Name** | `Company Portal` |
| **Publisher** | Microsoft Corporation |
| **Package identifier** | `9WZDNCRFJ3PZ` (populated by the Store picker) |
| **Installer type** | UWP (populated automatically) |
| **Install behavior** | **System** |
| **Category** | No category selected |
| **Show as a featured app** | No |

I kept the Store-provided description and other pre-populated app details. I didn't need to invent a download URL or upload an installer manually.

![Company Portal app information with System install behavior selected]({{ '/assets/images/endpoint-management/company-portal-system-install.png' | relative_url }})

**Why System?** Because we're assigning Company Portal to our corporate Autopilot **device group**. We want it installed as part of the managed-device setup rather than waiting for the employee to select it as a personal app.

### 3. Assign Company Portal as Required

I selected **Next**, continued through **Scope tags**, and opened **Assignments**.

Under **Required**, I selected **Add group** and added:

`SG-DEVICES-AUTOPILOT-CORPORATE`

For this Required assignment, I used:

| Assignment option | Value |
|---|---|
| **Group mode** | Included |
| **Group** | `SG-DEVICES-AUTOPILOT-CORPORATE` |
| **Assignment filter** | None |
| **Installation deadline** | **As soon as possible** |
| **Restart grace period** | Disabled |
| **Available for enrolled devices** | No assignment |
| **Uninstall** | No assignment |

![Company Portal Required assignment to the corporate Autopilot device group]({{ '/assets/images/endpoint-management/company-portal-required-assignment.png' | relative_url }})

I then selected **Next → Review + create → Create**. After creation, I could reopen it through:

**Apps → All apps → Company Portal → Properties → Assignments → Edit**

This is also where we'll verify or adjust its targeting if we change the pilot later.

At this point, **the app and its assignment exist**, but we haven't deployed our VM. We're not claiming it has installed on a Windows device yet.

![Company Portal Required assignment to the corporate Autopilot device group]({{ '/assets/images/endpoint-management/company-portal.png' | relative_url }})

### 4. Create Our Microsoft 365 Apps Baseline

Next, I returned to **Apps → All apps → Create**.

From the app-type selector, I chose **Windows → Microsoft 365 Apps → Windows 10 and later** (the exact category layout can vary between Intune portal versions).

Under **App suite information**, I entered:

| Field | Value used |
|---|---|
| **Suite name** | `Microsoft 365 Apps - Wire Finance Pilot` |
| **Description** | `Microsoft 365 Apps for Windows 10 and later` |
| **Publisher** | Microsoft |
| **Category** | Productivity |
| **Featured app** | No |

![Microsoft 365 Apps suite information for the Wire Finance pilot]({{ '/assets/images/endpoint-management/m365-apps-suite-information.png' | relative_url }})

For **Configure app suite**, I selected **Configuration designer** and chose these six apps:

- Word, Excel, PowerPoint and Outlook
- OneNote and Teams

I left **Access**, **Publisher**, and **Skype for Business** out of the final selection.

The remaining settings were:

| Configuration | Value used |
|---|---|
| **Architecture** | 64-bit |
| **Default file format** | Office Open XML Format |
| **Update channel** | **Current Channel** |
| **Remove other versions** | Yes |
| **Version to install** | Latest |
| **Shared computer activation** | No |
| **Accept Microsoft Software License Terms for users** | Yes |
| **Install background service for Microsoft Search in Bing** | Yes (left enabled in this pilot) |
| **Languages** | No additional language explicitly selected |

![Earlier Microsoft 365 Apps configuration before correcting the app selection and update channel]({{ '/assets/images/endpoint-management/m365-apps-initial-settings-before-correction.png' | relative_url }})

### 5. Assign Microsoft 365 Apps to Our Pilot Users

On the **Assignments** step, I added the **Required** user group:

`SG-PILOT-IDENTITY`

I didn't assign it to **All users**, and I didn't make it an Available or Uninstall app.

![Microsoft 365 Apps Required assignment to SG-PILOT-IDENTITY]({{ '/assets/images/endpoint-management/m365-apps-required-assignment.png' | relative_url }})

I then selected **Next → Review + create → Create**.

To return to its configuration later, the route is:

**Apps → All apps → Microsoft 365 Apps - Wire Finance Pilot → Properties**

From there we can inspect the app configuration and use **Edit** on **Assignments** if we need to adjust the group targeting.

**Why target users rather than the Autopilot device group?** All eight approved pilot employees are covered by `SG-PILOT-IDENTITY`, and their Microsoft 365 E5 licences have already been assigned. The device-level Company Portal baseline and user-level productivity baseline can therefore be managed separately.

### 6. Finish the ESP: Block on Company Portal Only

We're not quite finished. In Lesson 3, we created `WF-ESP-CORPORATE` with **All** required apps as the initial blocking choice, because Company Portal hadn't been created yet.

Now it exists and has its Required device assignment. So I returned to:

**Devices → Device onboarding → Enrollment → Enrollment Status Page → WF-ESP-CORPORATE → Properties → Settings → Edit**

I left the main ESP settings unchanged—including the **60-minute** timeout and **Block device use until all apps and profiles are installed = Yes**—and updated the required-app selection:

1. Find **Block device use until required apps are installed if they're assigned to the user/device**.
2. Change **All** to **Selected**.
3. Choose **Select apps**, search for **Company Portal**, select it, and confirm.
4. Verify that the **Blocking apps** list contains **Company Portal only**.
5. Set **Only fail selected blocking apps in technician phase** to **Yes**, matching our saved configuration. This option concerns Autopilot pre-provisioning; **we're using user-driven deployment**, not a technician pre-provisioning flow.
6. Select **Review + save → Save**, then reopen the ESP settings to confirm the choice remains in place.

![Final Enrollment Status Page blocking app selection: Company Portal only]({{ '/assets/images/endpoint-management/esp-final-company-portal-blocker.png' | relative_url }})

**Important:** adding Company Portal to the blocking list doesn't deploy it. That's why we first created its **Required** assignment. And although Microsoft 365 Apps is also Required, we're **not** making the full Office suite an ESP-blocking app.

That matters for this first pilot: Microsoft 365 Apps may continue installing after initial provisioning, and we don't want an Office installation to hold up the entire Windows OOBE experience. Microsoft also warns about possible installation-concurrency problems when Microsoft 365 Apps are tracked alongside certain Win32 installations during ESP.

### 7. Our Application Readiness Checkpoint

Before building the VM, our cloud-side application configuration is now:

| Check | Result |
|---|---|
| Company Portal created as Microsoft Store app (new) | **Yes** |
| Company Portal install behavior | **System** |
| Company Portal Required assignment | **`SG-DEVICES-AUTOPILOT-CORPORATE`** |
| Microsoft 365 Apps suite created | **Yes — six selected apps** |
| Microsoft 365 Apps update channel | **Current Channel** |
| Microsoft 365 Apps Required assignment | **`SG-PILOT-IDENTITY`** |
| ESP required-app blocking | **Selected → Company Portal only** |
| Device installation results | **Not yet tested in this walkthrough** |

This gives us the assignments needed for the first deployment without jumping ahead and claiming that Olivia has reached the Windows desktop or installed any of these applications.

**Next: Building Our First Windows 11 Virtual Machine — Lesson 6.** We'll create `WF-LT-001` in Oracle VirtualBox, install Windows 11 Pro, and stop at the first OOBE screen before registering the VM with Windows Autopilot.

---

**Microsoft references:** [Add the Windows Company Portal app](https://learn.microsoft.com/en-us/intune/app-management/deployment/add-company-portal-autopilot) · [Deploy Microsoft 365 Apps to Windows](https://learn.microsoft.com/en-us/intune/app-management/deployment/add-microsoft-365-windows) · [Configure the Enrollment Status Page](https://learn.microsoft.com/en-us/intune/device-enrollment/windows/setup-status-page)