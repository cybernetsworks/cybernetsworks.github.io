---
layout: lesson
title: "First Windows Autopilot Deployment — Olivia Carter"
series: wire-finance
parent: endpoint-device-management-implementation
order: 8
description: "Guide Olivia Carter through the first Wire Finance user-driven Autopilot sign-in, Microsoft Authenticator registration, Enrollment Status Page, and initial Company Portal and Microsoft 365 Apps checks."
---

We've built `WF-LT-001`, registered it with Windows Autopilot, confirmed the `CORPORATE` Group Tag, and verified that its device record shows **Profile status: Assigned** for `WF_AP_CORPORATE_USER_DRIVEN`.

Now it's time to test the experience from the employee's side.

**Can Olivia Carter sign in to a fresh Windows device and receive the Wire Finance-managed environment without IT manually configuring her desktop?**

This lesson takes us from the first corporate sign-in to a usable Windows desktop. We'll also complete Olivia's Microsoft Authenticator setup at the point it is requested—not beforehand—and check the initial application state. The deeper Entra and Intune record checks belong in Lesson 9.

### 1. Check Readiness Before Handing Over the Device

Our first pilot employee is **Olivia Carter, Finance Analyst**. Before continuing through OOBE, verify the prerequisites:

| Readiness check | Wire Finance configuration |
|---|---|
| User account | Olivia Carter — enabled standard Microsoft Entra user |
| Pilot membership | `SG-PILOT-IDENTITY` |
| Microsoft 365 licensing | E5 assigned through `SG-LICENCE-M365-E5` |
| Entra device join / automatic MDM scope | Pilot users permitted; MDM scope **Some → `SG-PILOT-IDENTITY`** |
| Autopilot device | `WF-LT-001` registered with Group Tag `CORPORATE` |
| Deployment profile | `WF_AP_CORPORATE_USER_DRIVEN` — **Assigned** on this device |
| Enrollment Status Page | `WF-ESP-CORPORATE`; Company Portal is the selected blocking app |
| Required device app | Company Portal — `SG-DEVICES-AUTOPILOT-CORPORATE` |
| Required user apps | `Microsoft 365 Apps - Wire Finance Pilot` — `SG-PILOT-IDENTITY` |

We also reset Olivia's temporary password earlier, with **Require this user to change their password when they first sign in** enabled. Have the current temporary password available through a secure handover method; do not put it in a screenshot, article or shared support ticket.

**Use Olivia's actual tenant sign-in name.** Wire Finance is still using its `onmicrosoft.com` domain in this lab. The planned custom domain is not yet our active sign-in domain.

### 2. Resume Windows OOBE and Connect to the Internet

In Lesson 7, we restarted the VM after confirming the Autopilot profile assignment. Return to Oracle VirtualBox and continue Windows setup:

1. Start or return to `WF-LT-001`.
2. If prompted, select the appropriate country/region and keyboard layout. These screens can vary based on networking and profile processing; do not assume that every OOBE prompt will be hidden.
3. Check that Windows has working outbound internet connectivity. Our original VM used NAT and Bridged adapters; **NAT alone is sufficient** for this scenario.
4. Continue until Windows retrieves the assigned corporate Autopilot experience and displays the Microsoft work-account sign-in.

If you only see a generic personal-PC setup path, **do not create a personal or local employee account to get past it**. Revisit the Autopilot device record, profile assignment and network connection before proceeding.

Our profile uses **User-driven** deployment and **Microsoft Entra joined**, so the employee signs in during onboarding. This is not self-deploying Autopilot or an on-premises domain join.

### 3. Sign In as Olivia Carter

At the organisational Microsoft sign-in page:

1. Enter **Olivia's Wire Finance work-account username**.
2. Enter the current temporary password that was prepared for her.
3. Follow any **change-password** prompt. The account was configured to require a new password at first sign-in, so choose a new password according to the tenant's applicable requirements.
4. Complete any additional identity-verification prompts shown during this sign-in.

Do not use the administrative identity that uploaded the Autopilot hardware hash. That account was used for device registration only. **Olivia's own identity must be the one used for the user-driven deployment.**

We intentionally avoided signing Olivia into another machine ahead of time. This lets us document her first interactive experience as part of corporate Windows onboarding.

### 4. Register Microsoft Authenticator During the Sign-In

During Olivia's onboarding, Microsoft Authenticator was set up as part of her actual sign-in experience. This is the point in our learning journey where it belongs—not back in Chapter 7, when we were discussing MFA readiness.

If Windows or Microsoft Entra asks the employee to set up Microsoft Authenticator, follow the instructions presented:

1. Install or open the official **Microsoft Authenticator** app on the employee's phone.
2. In Authenticator, choose **Add account (+) → Work or school account**.
3. If the Windows sign-in presents a QR code, choose **Scan a QR code** in the app and scan the displayed code. If a different supported registration flow appears, follow that flow instead.
4. Complete the displayed verification or test approval and return to Windows setup.
5. Confirm the registration flow completes before continuing with provisioning.

**Important security distinction:** registering Microsoft Authenticator during sign-in does **not**, by itself, prove that our own Conditional Access MFA policy has been created or enforced. Wire Finance's broader Conditional Access and authentication-method enforcement model is still a later implementation task. When we validate authentication requirements, we'll inspect the real sign-in logs and the policies that applied.

We have not included a screenshot of Olivia's QR code or the authentication approval screens. These were not captured as publication-ready evidence, and a real registration QR code or approval detail should not be reused in a public tutorial.

For current instructions covering different Authenticator registration experiences, see [Microsoft's work or school account setup guide](https://support.microsoft.com/en-us/authenticator/how-to-add-your-accounts-to-microsoft-authenticator).

### 5. Let the Enrollment Status Page Finish

Once Olivia is authenticated, Windows continues the corporate provisioning process. **Do not close, skip or work around the Enrollment Status Page (ESP).**

Microsoft's user-driven deployment normally tracks three stages:

```text
Device preparation
       ↓
Device setup
       ↓
Account setup
       ↓
Windows desktop
```

The first two stages prepare and configure the device; the account phase handles user-targeted work. Some screens may be brief or change between Windows versions, and a restart can require the employee to sign in again.

For Wire Finance, `WF-ESP-CORPORATE` is configured with a **60-minute timeout**, progress display and diagnostic access. Its final required-app blocker is **Selected → Company Portal**. Microsoft 365 Apps is assigned as **Required**, but it is **not selected as an ESP-blocking application**.

This distinction is deliberate: Company Portal must be installed as part of our initial device baseline, while the full Microsoft 365 suite can continue installing after Windows becomes usable.

Our first pilot completed the core provisioning journey and reached the Windows desktop. This establishes the initial onboarding milestone; it does not prove that every application and security policy has finished installing.

### 6. Check Company Portal on the New Desktop

Once Windows reaches the desktop:

1. Open **Start** and launch **Company Portal**.
2. If prompted, authenticate with Olivia's work account.
3. Open the device area in Company Portal and identify **This device**.
4. Check the displayed device name and resource-access message.

Our initial deployment showed the following:

![Company Portal showing DESKTOP-QDSCPUK as This device and Can access company resources]({{ '/assets/images/endpoint-management/olivia-company-portal-device-access.png' | relative_url }})

The Windows hostname was **`DESKTOP-QDSCPUK`**, while the VirtualBox VM and asset record remain **`WF-LT-001`**. This is expected because the Autopilot deployment profile has **Apply device name template = No**.

The Company Portal message is a useful positive signal, but **it is not a complete security or compliance assessment**. We'll confirm the underlying Entra join and Intune management records in Lesson 9.

### 7. Inspect the Initial Application Deployment

In Company Portal, open **Downloads & updates** to examine which managed applications are installed or pending.

![Company Portal Downloads and updates showing Company Portal installed and Microsoft 365 Apps pending]({{ '/assets/images/endpoint-management/olivia-company-portal-app-status.png' | relative_url }})

This tells us two different things:

- **Company Portal** had installed successfully through its device-level Required assignment.
- **Microsoft 365 Apps - Wire Finance Pilot** had entered the deployment queue through its user-level Required assignment. The screenshot does **not** establish that every Office application finished installing.

Outlook and Teams were also observed on the first desktop during the lab. Their visibility is encouraging, but we still need to verify the complete six-app baseline and installation status in the next lesson.

Now return to **Company Portal → Home**. You may see a message that no applications have been made available for self-service installation:

![Company Portal Home showing no recently published apps available to Olivia]({{ '/assets/images/endpoint-management/olivia-company-portal-no-available-apps.png' | relative_url }})

### 8. What We Have Proven — and What Comes Next

| Check | Result from our first pilot |
|---|---|
| Corporate user-driven Autopilot flow | **Completed** |
| Olivia's first organisational sign-in | **Completed** |
| Microsoft Authenticator setup during onboarding | **Completed**; not proof of a deployed Wire Finance Conditional Access MFA policy |
| Microsoft Entra join and automatic Intune enrollment | **Core provisioning completed**; detailed portal-record validation follows |
| `WF-ESP-CORPORATE` | **Provisioning completed** |
| Company Portal installation | **Confirmed** |
| Company Portal resource-access message | **Can access company resources** |
| Windows hostname | **`DESKTOP-QDSCPUK`**, not the asset ID `WF-LT-001` |
| Microsoft 365 Apps | **Required deployment queued; full installation not yet verified** |
| Endpoint security, BitLocker, Defender onboarding, compliance and Conditional Access enforcement | **Not implemented or verified as part of this milestone** |

The milestone is **`WF-ENDPOINT-PILOT-001` — core provisioning successful**. We've demonstrated that the Autopilot flow can take a fresh Windows VM through employee authentication and into a usable, Intune-managed experience.

**A usable desktop is not the finish line.** Before expanding our pilot, we need to check the actual Entra and Intune records, primary user, last check-in, Autopilot association and complete application status. We also still need to implement the endpoint security and compliance controls.

### Common Issues and Where to Check

| Symptom | Initial check |
|---|---|
| Corporate sign-in doesn't appear | Confirm internet access and that the individual Autopilot device shows **Profile status: Assigned** for `WF_AP_CORPORATE_USER_DRIVEN` |
| Olivia cannot sign in | Check the actual tenant username, current password, account enabled state and `SG-PILOT-IDENTITY` membership; review the sign-in error without publishing credentials |
| Authenticator registration doesn't complete | Follow the exact prompt, confirm the phone has connectivity, and use the approved authentication-method recovery process rather than bypassing verification |
| ESP times out | Use ESP diagnostic information; review targeted policies and Company Portal's Required installation status. Do not bypass the error without investigation |
| Microsoft 365 Apps remains pending | Check its Required user assignment and installation status in Intune; do not assume ESP should wait for Office, since Office was not selected as a blocker |
| Company Portal lists no Available apps | Expected until apps are explicitly assigned as **Available** |
| Windows hostname doesn't match `WF-LT-001` | Expected. `WF-LT-001` is our asset/VM identity; the Autopilot name template was disabled |

**Next: Post-Deployment Validation — Lesson 9.** We'll verify the Microsoft Entra device record, Intune managed-device record, primary user, check-in history, Autopilot association and final application results before moving on to endpoint hardening.

---

**Microsoft references:** [Deploy a user-driven Microsoft Entra joined Autopilot device](https://learn.microsoft.com/en-us/autopilot/tutorial/user-driven/azure-ad-join-deploy-device) · [User-driven deployment overview](https://learn.microsoft.com/en-us/autopilot/user-driven) · [Microsoft Authenticator for work or school accounts](https://support.microsoft.com/en-us/authenticator/how-to-add-your-accounts-to-microsoft-authenticator)