---
layout: lesson
title: "Automatic MDM Enrollment and Microsoft Entra Join"
series: wire-finance
parent: endpoint-device-management-implementation
order: 2
description: "Configure the Wire Finance pilot's two enrollment prerequisites: permit selected employees to join Windows devices to Microsoft Entra ID, then automatically enroll those devices into Microsoft Intune."
---

Our custom Intune administrator role and pilot assignment are now in place.

But being **allowed to manage devices** is different from having **devices enrolled to manage**.

Before we prepare our Windows Autopilot experience, we need to configure two things: **who can join a Windows device to Wire Finance, and whose device should automatically enroll in Intune?**

These sound like one setting. They aren't.

### 1. Two Separate Gates

```text
Olivia signs in to her future corporate Windows device
                       ↓
Microsoft Entra device-join permission
Can this user join a device to our tenant?
                       ↓
Automatic Intune MDM enrollment scope
Should this user's device enroll in Intune?
                       ↓
A joined and managed device — once provisioning succeeds
```

**Microsoft Entra Join** gives Windows an organisational device identity. **Intune enrollment** establishes its management relationship.

Neither setting, by itself, proves that a device has completed both steps—or that it is secure or compliant.

For our pilot, we'll use the **Assigned user group** created earlier:

`SG-PILOT-IDENTITY`

That gives us a controlled starting population rather than opening this onboarding route to every employee at once.

### 2. Configure Automatic MDM Enrollment in Intune

I opened the [Microsoft Intune admin center](https://intune.microsoft.com/) and followed the navigation displayed in our tenant:

**Devices → Device onboarding → Enrollment → Automatic enrollment**

Some Microsoft documentation shows a **Windows** tab between *Enrollment* and *Automatic enrollment*. Our tenant exposed **Automatic enrollment** directly, so I followed the route shown in the portal.

On the automatic-enrollment page, I configured:

| Setting | Value used |
|---|---|
| **MDM user scope** | **Some** |
| **Groups** | `SG-PILOT-IDENTITY` |
| **WIP/MAM user scope** | **None** |
| **MDM terms of use URL** | Left at the portal-provided default |
| **MDM discovery URL** | Left at the portal-provided default |
| **MDM compliance URL** | Left at the portal-provided default |

To reproduce the configuration:

1. Under **MDM user scope**, select **Some** rather than **All**.
2. Open the **Groups** selector, find `SG-PILOT-IDENTITY`, select it and confirm your selection.
3. Leave the **WIP/MAM user scope** at **None** for this device-management pilot.
4. Keep the existing Microsoft Intune MDM URLs unchanged; we're not using custom enrollment endpoints.
5. Select **Save**.

The configuration saved successfully.

**Why Some?** Because `SG-PILOT-IDENTITY` contains the employees deliberately approved for early testing. We're targeting a user population, not enrolling the entire company at once.

**Why None for WIP/MAM?** Our immediate focus is **managing corporate Windows devices through Intune**, rather than introducing a separate application-management or BYOD protection route. We can evaluate those controls later.

Microsoft reference: [Set up automatic enrollment for Windows devices](https://learn.microsoft.com/en-us/intune/device-enrollment/windows/enable-automatic-mdm).

### 3. Separately Allow Pilot Users to Join Microsoft Entra

Saving the Intune scope didn't finish the job.

Our pilot employees also need permission to join Windows devices to the organisation.

I opened the [Microsoft Entra admin center](https://entra.microsoft.com/) and navigated to:

**Identity → Devices → Overview → Device settings**

Under **Users may join devices to Microsoft Entra ID**, I used:

| Setting | Value used |
|---|---|
| **Users may join devices to Microsoft Entra ID** | **Selected** |
| **Selected users/groups** | `SG-PILOT-IDENTITY` |

To reproduce the setting:

1. Find **Users may join devices to Microsoft Entra ID**.
2. Choose **Selected**, rather than **All**.
3. Open the selection control and add `SG-PILOT-IDENTITY`.
4. Confirm the selection and select **Save**.
5. Reopen **Device settings** to check that the selected group remains in place.

This is a **separate control in a separate portal**. The Entra setting permits the device join; the Intune setting controls automatic MDM enrollment for the scoped users.

Microsoft reference: [Manage device identities in Microsoft Entra ID](https://learn.microsoft.com/en-us/entra/identity/devices/manage-device-identities).

### 4. Keep User Enrollment and Device Targeting Separate

We've created groups for both users and devices, but they serve different purposes:

| Group | What it controls |
|---|---|
| `SG-PILOT-IDENTITY` | **Which employees** can participate in the selected Entra join and automatic MDM enrollment scopes |
| `SG-DEVICES-WINDOWS-PILOT` | **Which devices** we explicitly include in our pilot administration scope |
| `SG-DEVICES-AUTOPILOT-CORPORATE` | **Which Autopilot-classified devices** receive our corporate provisioning policies |

Only **`SG-PILOT-IDENTITY`** was selected for the two user-oriented settings configured in this lesson.

The corporate Autopilot group is about to become important. In the **next lesson**, we'll assign our Enrollment Status Page profile, `WF-ESP-CORPORATE`, to `SG-DEVICES-AUTOPILOT-CORPORATE`. We'll also target that group with our Autopilot deployment profile in Lesson 4.

**Assigning a profile to the group does not mean a device is already in it.** Our first VM won't be built until Lesson 6 or registered with the `CORPORATE` Group Tag until Lesson 7. That is when we'll check the device's dynamic group membership and profile assignment.

### 5. Our Configuration Checkpoint

Before moving on to the Enrollment Status Page, these are the settings we've put in place:

| Check | Result |
|---|---|
| MDM user scope = **Some** | **Configured and saved** |
| `SG-PILOT-IDENTITY` selected for automatic enrollment | **Configured and saved** |
| WIP/MAM user scope = **None** | **Configured and saved** |
| Entra join permission = **Selected**, targeting `SG-PILOT-IDENTITY` | **Configured and saved** |
| First Windows device joined and managed by Intune | **Not yet tested in the website journey** |

We already have the employee identities, group memberships and Microsoft 365 E5 licences from Chapter 7. Those are essential prerequisites, but they don't replace a real device-enrollment test.

If the first deployment doesn't enroll as expected, we'll return to these controls and check the user's group membership, licensing, enrollment restrictions and any relevant device limits. For now, **the configuration is prepared; the endpoint test comes later**.

### Next: Enrollment Status Page Configuration

We've established **who can join a device** and **who can trigger automatic Intune enrollment**.

Next, we need to prepare what those users will experience while Windows is being provisioned.

We'll create **`WF-ESP-CORPORATE`**, decide how setup progress and errors are displayed, and set the initial app-blocking behaviour. After we create Company Portal in Lesson 5, we'll refine that blocking selection before building the VM.

**Next: Enrollment Status Page Configuration — Lesson 3.**