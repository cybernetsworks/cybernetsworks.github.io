---
layout: lesson
title: "Windows Autopilot Deployment Profile"
series: wire-finance
parent: endpoint-device-management-implementation
order: 4
description: "Create Wire Finance's user-driven Windows Autopilot deployment profile, configure the Microsoft Entra join experience, and target corporate devices before building the first VM."
---

Our Enrollment Status Page is ready. It will show setup progress and control when an employee can use a newly provisioned Windows device.

But there's another question to answer before Olivia receives her workstation:

**How will Windows know that this is a Wire Finance device, which organisation it should join, and what kind of account Olivia should have on it?**

That's where the **Windows Autopilot deployment profile** comes in.

The Enrollment Status Page controls *setup progress and blocking*. The Autopilot profile controls *how the device goes through corporate Windows setup*. We need both, but they do different jobs.

### 1. Create Our Corporate Autopilot Profile

I opened the [Microsoft Intune admin center](https://intune.microsoft.com/) and navigated to:

**Devices → Device onboarding → Enrollment → Windows Autopilot → Deployment profiles**

Depending on your Intune navigation, you may first select **Windows** and then **Enrollment**. Once in **Deployment profiles**, select:

**Create profile → Windows PC**

Under **Basics**, I used:

| Setting | Value |
|---|---|
| **Name** | `WF_AP_CORPORATE_USER_DRIVEN` |
| **Description** | Provides the standard Wire Finance user driven Windows Autopilot deployment experience for corporate Windows endpoints using Microsoft Entra Join and automatic Intune enrollment. |
| **Convert all targeted devices to Autopilot** | **No** |

The original design used a hyphenated profile name, but the name I ultimately saved in Intune uses underscores: **`WF_AP_CORPORATE_USER_DRIVEN`**. That's the name to use throughout the implementation.

I set **Convert all targeted devices to Autopilot = No** because we'll explicitly register our first VM using its hardware identity and apply the `CORPORATE` Group Tag. We aren't trying to bulk-register existing Intune-managed computers simply by assigning this profile.

Then I selected **Next**.

### 2. Configure the Windows Out-of-Box Experience

On **Out-of-box experience (OOBE)**, I entered the following settings:

| Setting | Our configuration |
|---|---|
| **Deployment mode** | **User-Driven** |
| **Join to Microsoft Entra ID as** | **Microsoft Entra joined** |
| **Language (Region)** | **Operating system default** |
| **Automatically configure keyboard** | **Yes** |
| **Microsoft Software License Terms** | **Hide** |
| **Privacy settings** | **Hide** |
| **Hide change account options** | **Hide** |
| **User account type** | **Standard** |
| **Allow pre-provisioned deployment** | **No** |
| **Apply device name template** | **No** |

These weren't arbitrary settings. Each one helps shape the experience we want our first employee to follow.

**User-driven, not self-deploying.** Olivia will authenticate with her own Wire Finance identity during Windows setup. We want to observe that interaction, including the Microsoft Authenticator registration we discussed in Chapter 7. We're not using an unattended or technician-led pre-provisioning flow.

**Microsoft Entra joined, not hybrid joined.** Our initial Wire Finance tenant is cloud-first. We have no deployed on-premises Active Directory domain or domain controllers for this Windows workstation to join.

**Standard user, not local administrator.** Olivia needs a productive workstation, not routine local administrative privileges. Selecting **Standard** helps maintain that boundary during Autopilot onboarding.

**Simplified OOBE.** I selected **Hide** for Microsoft Software License Terms, privacy settings, and change-account options, and kept the OS's default language while enabling automatic keyboard configuration. Those options reduce unnecessary setup interaction where supported. They don't remove the need for Olivia to sign in. The change-account hiding behaviour also depends on Entra company branding, which we'll verify during actual deployment rather than assume.

There is a small operational consideration with hiding privacy settings: some privacy-related choices—including location services—can be affected. If a business later needs location services, we'll manage those settings through an appropriate policy rather than assume they're enabled.

### 3. Why I Did Not Apply a Device Name Template

This is one setting worth understanding before we proceed.

Our device naming convention uses an asset identifier such as:

`WF-LT-001`

But **Windows Autopilot's native naming templates do not automatically allocate a sequential asset number**. They support patterns using values such as `%SERIAL%` or `%RAND:x%`, not our controlled `001`, `002`, `003` asset sequence.

So I selected:

**Apply device name template = No**

That means the Windows computer name may be generated during setup. We'll inspect the actual hostname later and keep it distinct from the `WF-LT-001` asset record.

**An asset identifier and a Windows hostname serve related purposes, but they don't have to be identical.** We won't claim our naming convention has been automatically enforced when it hasn't.

### 4. Assign the Profile to the Corporate Autopilot Group

Next, I opened **Assignments** and added this group under **Included groups**:

`SG-DEVICES-AUTOPILOT-CORPORATE`

I didn't target **All devices**, and I didn't add our standard-user pilot group here. The profile needs to reach the **corporate Autopilot device population**.

We created this dynamic device group in the earlier Entra implementation work. Its membership rule looks for the Autopilot Group Tag `CORPORATE`:

```text
(device.devicePhysicalIds -any (_ -eq "[OrderID]:CORPORATE"))
```

Our intended relationship is:

```text
Autopilot registration with Group Tag CORPORATE
                       ↓
Dynamic membership evaluation
SG-DEVICES-AUTOPILOT-CORPORATE
                       ↓
Assigned deployment profile
WF_AP_CORPORATE_USER_DRIVEN
                       ↓
Corporate user-driven OOBE (later)
```

**At this stage, we're assigning the profile to a group, not to an actual VM.** We haven't built or registered `WF-LT-001` yet in the website journey. When we register it in Lesson 7, we'll check that the group rule includes it and that the Autopilot device itself reports the correct assigned profile.

We also left explicit **Autopilot device-to-user assignment** for later rather than pre-assigning Olivia to a hardware record. That optional association isn't required for her to sign in during a user-driven deployment.

I reviewed the selections and clicked **Create**.

### 5. Verify the Saved Profile and Assignment

Back in **Windows Autopilot deployment profiles**, the new profile appeared with:

- **Name:** `WF_AP_CORPORATE_USER_DRIVEN`
- **Join type:** Microsoft Entra joined
- **Assigned:** Yes

![Saved Wire Finance Windows Autopilot deployment profile in Intune showing its name, join type, and assigned status]({{ '/assets/images/endpoint-management/windows-ap-corporate-user-driven-confirmation.png' | relative_url }})

I then opened the profile to check the saved settings and group assignment rather than relying only on the confirmation screen.

![Saved Wire Finance Autopilot profile properties showing user-driven Entra join, Standard user, naming-template choice, and active corporate group assignment]({{ '/assets/images/endpoint-management/wf-ap-corporate-assign-confirmation.png' | relative_url }})

One distinction is important: **Assigned = Yes on the profile list means the profile has an assignment**. It does **not** yet prove our future VM has received it. We'll look for **Profile status = Assigned** on the *specific Autopilot device record* before Olivia begins provisioning.

### Our Checkpoint

| What we checked | Result |
|---|---|
| `WF_AP_CORPORATE_USER_DRIVEN` created | **Yes** |
| User-driven, Microsoft Entra joined | **Configured** |
| User account type | **Standard** |
| Pre-provisioning | **Disabled** |
| Device name template | **Disabled** |
| `SG-DEVICES-AUTOPILOT-CORPORATE` included | **Active** |
| The first VM registered and assigned this profile | **Not yet — Lessons 6 and 7** |
| Olivia's actual Windows sign-in | **Not yet — Lesson 8** |

We now have the configuration that tells an eligible corporate device how to move through Windows setup. But before we build and register that device, it still needs its applications.

**Next: Company Portal and Microsoft 365 Apps Deployment — Lesson 5.** We'll create the required app assignments, then return to `WF-ESP-CORPORATE` and change the initial **All required apps** blocker to **Selected → Company Portal**.

---

**Microsoft references:** [Configure Windows Autopilot profiles](https://learn.microsoft.com/en-us/autopilot/profiles) · [User-driven Microsoft Entra join: Create and assign the Autopilot profile](https://learn.microsoft.com/en-us/autopilot/tutorial/user-driven/azure-ad-join-autopilot-profile)