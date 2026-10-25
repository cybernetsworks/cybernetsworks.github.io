---
layout: lesson
title: "Enrollment Status Page Configuration"
series: wire-finance
parent: endpoint-device-management-implementation
order: 3
description: "Create Wire Finance's Windows Autopilot Enrollment Status Page, configure setup progress and blocking behaviour, and assign it to our corporate Autopilot device group."
---

We've configured who can join Windows devices to Microsoft Entra ID and who can automatically enroll them into Intune.

But when Olivia eventually signs in to her new workstation, how will she know whether setup is still running? And should Windows let her start working before the essential configuration has finished?

That's the job of the **Enrollment Status Page (ESP)**.

For Wire Finance, I created a custom profile named **`WF-ESP-CORPORATE`**. It will show provisioning progress and hold the device at setup while the required blocking work completes. We aren't building the VM or signing Olivia in yet—first, we're preparing the experience that device will receive.

### 1. Open the Enrollment Status Page

I signed in to the [Microsoft Intune admin center](https://intune.microsoft.com/) and followed the navigation shown in our tenant:

**Devices → Device onboarding → Enrollment → Enrollment Status Page**

Depending on the portal layout, you might first select the **Windows** tab and find **Enrollment Status Page** under **Windows Autopilot**.

I selected **Create** to make a separate corporate profile instead of modifying the Microsoft default one.

Under **Basics**, I entered:

| Field | Value |
|---|---|
| **Name** | `WF-ESP-CORPORATE` |
| **Description** | Corporate Windows Autopilot Enrollment Status Page for the Wire Finance pilot. |

The custom profile lets us target the correct device population without making these settings apply indiscriminately to the entire tenant.

### 2. Configure the Provisioning Experience

On **Settings**, I used the following values:

| ESP setting | Selection |
|---|---|
| **Show app and profile configuration progress** | **Yes** |
| **Show an error when installation takes longer than specified number of minutes** | **60** |
| **Show custom message when time limit or error occurs** | **Yes** |
| **Turn on log collection and diagnostics page for end users** | **Yes** |
| **Only show page to devices provisioned by out-of-box experience (OOBE)** | **Yes** |
| **Install Windows updates (might restart the device)** | **No** |
| **Block device use until all apps and profiles are installed** | **Yes** |
| **Allow users to reset device if installation error occurs** | **No** |
| **Allow users to use device if installation error occurs** | **No** |
| **Block device use until required apps are installed if assigned to the user/device** | **All — initial setting** |

For the error message, I kept the wording shown in the portal:

> Setup could not be completed. Please try again or contact your support person for help.

![Initial WF-ESP-CORPORATE settings with installation progress, 60-minute timeout, and All required apps selected]({{ '/assets/images/endpoint-management/esp-initial-all-required-apps.png' | relative_url }})

A few choices deserve explanation.

**Progress = Yes:** Olivia should see that corporate setup is taking place instead of being left at an unexplained screen.

**60-minute timeout:** this is our starting limit for the pilot, not a promise that every application will finish in an hour. If setup takes too long, we'll investigate the cause rather than blindly increasing the timeout.

**Diagnostics = Yes:** if setup fails, we want a way to collect useful troubleshooting information.

**OOBE-only = Yes:** this keeps the ESP focused on initial device setup rather than showing the user portion to every later employee who signs in to the same computer for the first time.

**Windows updates during OOBE = No:** we're not adding that extra installation-and-restart stage to this first provisioning test. This does **not** disable normal Windows updating after deployment; update management is a separate future task.

**No reset or bypass on error:** if the required setup fails, we don't want the employee to work around the failure by pressing past it. IT can investigate and decide the recovery action.

### 3. Why I Started with “All” Required Apps

The final Wire Finance design is to block on **Company Portal only**, not on every Microsoft 365 application. But at this point in our revised walkthrough, we haven't created the Company Portal app in Intune yet.

**You cannot select an Intune app that doesn't exist.**

So I initially kept **All** in the required-app blocking selector while creating the ESP profile. After we create and assign Company Portal in **Lesson 5**, we'll return here and change:

```text
Block required apps: All
                 ↓
Block required apps: Selected
Selected blocking app: Company Portal
```

That later change is important. **Microsoft 365 Apps will still be assigned as Required**, but we won't make the full Office installation a condition for leaving ESP during our first pilot.

Also, the blocking-app picker does **not** deploy software by itself. The app must have its own **Required** assignment to the appropriate user or device group. We'll set up those assignments in Lesson 5.

### 4. Assign `WF-ESP-CORPORATE` to Our Autopilot Group

### 4. Assign `WF-ESP-CORPORATE` to Our Autopilot Group

After saving the ESP profile, I needed to make sure it targeted the correct corporate devices.

**Creating the ESP settings alone isn't enough; the profile also needs an assignment.**

I used this path in the [Microsoft Intune admin center](https://intune.microsoft.com/):

**Devices → Device onboarding → Enrollment → Enrollment Status Page → WF-ESP-CORPORATE → Properties → Assignments → Edit**

To reproduce the assignment:

1. Under **Enrollment Status Page**, open **`WF-ESP-CORPORATE`**.
2. Select **Properties**, scroll to **Assignments**, and select **Edit**.
3. Under **Included groups**, select **Add groups**.
4. Search for and select **`SG-DEVICES-AUTOPILOT-CORPORATE`**, then confirm the selection.
5. Check that the group appears under **Included groups** without an unintended assignment filter or exclusion.
6. Select **Review + save**, review the changes, and select **Save**. Return to **Properties** to verify the saved assignment.

Depending on the Intune portal view, you might first open the **Windows** tab under Enrollment.

I reopened the ESP properties and confirmed that the group appeared under **Included groups** with **Status: Active**. The scope tag remained **Default**.

![Saved ESP assignment showing SG-DEVICES-AUTOPILOT-CORPORATE as an active included group]({{ '/assets/images/endpoint-management/esp-assignment-confirmation.png' | relative_url }})

*Figure: The saved `WF-ESP-CORPORATE` assignment showing `SG-DEVICES-AUTOPILOT-CORPORATE` as Active, with no assignment filter and the Default scope tag.*

This is the **dynamic device group** we created earlier. Its membership rule identifies devices registered with the Autopilot Group Tag `CORPORATE`:

```text
(device.devicePhysicalIds -any (_ -eq "[OrderID]:CORPORATE"))
```

We're assigning the **ESP profile to the group**, not directly to Olivia or to a VM that hasn't been registered yet.

When we register `WF-LT-001` in Lesson 7, we'll check its group membership and policy targeting before beginning corporate Windows setup.

The saved profile appeared in the ESP list with **priority 1**. The Intune **Default** ESP remained available as the fallback when no other applicable ESP profile takes precedence.

This assignment is separate from our Autopilot **deployment profile**.

The ESP governs setup progress and blocking; the deployment profile we'll create next governs how Windows starts its corporate onboarding journey.

### 5. Verify What We've Prepared

Before leaving Intune, I checked the configuration we could validate without having a Windows device enrolled:

| Check | Result |
|---|---|
| Custom profile `WF-ESP-CORPORATE` | **Created** |
| Target group | **`SG-DEVICES-AUTOPILOT-CORPORATE`** |
| ESP profile priority | **1** |
| Progress and diagnostics | **Enabled** |
| Timeout | **60 minutes** |
| Device-use blocking | **Enabled** |
| Initial required-app blocking | **All** |
| Final Company Portal-only blocking | **To be configured in Lesson 5** |
| ESP shown on Olivia's device | **Not tested yet** |

One more setting appears after switching to **Selected**: **Only fail selected blocking apps in technician phase**. In our final settings this is **Yes**, but it concerns **Autopilot pre-provisioning's technician phase**, not the user-driven VM deployment we're using. We'll show that final selection when we edit the ESP in Lesson 5.

**The key distinction:** we have created and assigned the policy, but we haven't yet seen how it behaves on the device. That evidence comes during Olivia's actual Autopilot deployment.

### Next: Configure the Windows Autopilot Deployment Profile

Our Enrollment Status Page is in place. Now we need to tell Windows *how* to provision the device: which join type to use, whether Olivia becomes a local administrator, and which devices should receive that configuration.

**Next: Windows Autopilot Deployment Profile — Order 4.**

---

**Microsoft references:** [Set up the Enrollment Status Page](https://learn.microsoft.com/en-us/intune/device-enrollment/windows/setup-status-page) · [User-driven Autopilot: Configure and assign ESP](https://learn.microsoft.com/en-us/autopilot/tutorial/user-driven/azure-ad-join-esp)