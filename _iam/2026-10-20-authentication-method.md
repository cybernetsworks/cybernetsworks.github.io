---
layout: lesson
title: "Authentication Methods"
series: wire-finance
parent: entra-id-implementation
order: 6
description: "Implementing the initial Wire Finance authentication process, including temporary passwords, first-sign-in requirements, and preparation for Windows Autopilot."
---

We have created our first eight employees, established their group memberships, and started implementing administrative access.

But there is another important question.

**How do these employees actually sign in?**

Creating an identity in Microsoft Entra ID gives an employee an organisational account.

Group membership determines the access that account may receive.

But before an employee can use the account, we need to establish an authentication process.

For Wire Finance, we begin with temporary passwords and a controlled first-sign-in experience.

### Establishing the Initial Authentication Process

During user creation, the eight standard workforce identities were provisioned with temporary passwords.

Each employee was required to change their password at their first interactive sign-in.

Our initial authentication process follows this model:

```text
Employee Account Created
          ↓
Temporary Password Assigned
          ↓
Employee Receives Credentials
          ↓
First Organisational Sign-In
          ↓
Password Change Required
          ↓
Employee Establishes New Password
```

The purpose is to give employees a starting credential without expecting them to continue using the password originally assigned during provisioning.

However, this is only the initial authentication process.

**A password change is not the same as multi-factor authentication.**

Our stronger authentication controls will be introduced and validated separately.

### Preparing Olivia Carter for Her First Sign-In

Our first practical authentication test involved Olivia Carter, the Finance Analyst.

Olivia was selected as the first employee to complete the Windows Autopilot deployment journey.

Her organisational account had already been created and configured with the expected attributes, group memberships, and Microsoft 365 E5 licensing.

Before beginning the deployment, we needed to make sure her account was ready for its first organisational sign-in.

One option would have been to sign Olivia into an existing Windows computer to complete the initial password change.

But that would mean her first organisational sign-in happened outside the endpoint onboarding process we wanted to test.

Instead, I chose to preserve that first-sign-in experience for Windows Autopilot.

This meant preparing a fresh temporary password immediately before the device handover.

### Resetting Olivia's Temporary Password

I used the **Microsoft 365 admin center** to reset Olivia's password.

The navigation used was:

```text
Microsoft 365 Admin Center
          ↓
Users
          ↓
Active users
          ↓
Select Olivia Carter
          ↓
Reset password
```

### Step 1 — Configure the Password Reset

After selecting Olivia's account, I opened the password-reset panel.

The following settings were selected:

| Setting | Configured Value |
|---|---|
| User account | Olivia Carter |
| Account type | Standard workforce identity |
| Automatically create a password | Yes |
| Require password change at first sign-in | Yes |
| Intended first sign-in | Windows Autopilot |

![Olivia Carter password reset settings]({{ '/assets/images/active-directory/user-password-change-required.png' | relative_url }})

*Figure 1: Microsoft 365 admin center showing automatic password generation and the required first-sign-in password change.*

These two settings were important.

Automatically generating a password provided a fresh temporary credential.

Requiring a password change ensured Olivia would not continue using that temporary credential as her permanent password.

### Step 2 — Complete the Password Reset

With both options selected, I clicked **Reset password**.

Microsoft 365 confirmed that the operation completed successfully.

![Password reset confirmation]({{ '/assets/images/active-directory/password-reset-confirm.png' | relative_url }})

*Figure 2: Confirmation that Olivia Carter's password was successfully reset. The generated password is masked.*

This confirmed that the temporary credential had been reset and was ready for the upcoming onboarding test.

The actual password is deliberately excluded from this documentation.

This temporary password will be sent to Olivia for her first login.

In a real organisation, temporary credentials should be delivered to employees through an approved secure channel rather than exposed in screenshots, tickets, or public documentation.

### Why Generate a Fresh Password?

Olivia's identity already existed.

We were not creating another account.

We were preparing her existing account for a specific onboarding event.

```text
Olivia Carter
Existing Entra Identity
          ↓
Fresh Temporary Password
          ↓
Password Change Required
          ↓
First Autopilot Sign-In
```

This gave us a useful distinction between **account creation** and **account handover**.

Creating the identity is an administrative task.

The employee's first authentication is a separate event that needs to be validated.

### Authentication During Windows Autopilot

With Olivia's credentials prepared, we continued to our first Windows Autopilot deployment.

The device used the user-driven deployment profile:

`WF_AP_CORPORATE_USER_DRIVEN`

The onboarding journey involved:

```text
Windows Autopilot OOBE
          ↓
Organisational Sign-In
          ↓
Microsoft Entra Authentication
          ↓
First-Sign-In Requirements
          ↓
Microsoft Entra Join
          ↓
Automatic Intune Enrollment
          ↓
Endpoint Provisioning
```

Olivia subsequently completed the core user-driven Autopilot deployment successfully.

The device joined Microsoft Entra ID, enrolled into Microsoft Intune, and progressed through the required provisioning stages.

This confirmed that her organisational identity could participate in the endpoint onboarding process.

The detailed Autopilot configuration, Enrollment Status Page, application deployment, and endpoint validation will be covered in the endpoint implementation chapter.

For this page, the important result is that **Olivia's prepared identity was successfully used during the first organisational device onboarding test**.

### What About Self-Service Password Reset?

During this phase, I also considered **Self-Service Password Reset (SSPR)**.

SSPR allows users to reset their own passwords using supported verification methods without depending entirely on an administrator.

This can reduce support requests and improve account recovery.

Interestingly, after completing Olivia's password reset, Microsoft 365 also displayed a recommendation to enable self-service password reset.

However, I deliberately deferred SSPR during the initial pilot.

Our immediate priorities were:

- Establishing the workforce identities
- Validating initial authentication
- Confirming group membership and licensing
- Testing the Windows Autopilot onboarding process
- Completing the initial Intune enrollment

Introducing a separate password recovery workflow would have expanded the scope of the pilot.

SSPR remains a future authentication-hardening activity requiring its own configuration and validation.

**It has been considered, but it has not yet been implemented for the Wire Finance pilot.**

### Authentication Implementation Status

At this checkpoint, we can distinguish what has been configured from what remains to be implemented.

| Authentication Area | Current Status |
|---|---|
| Eight standard user accounts | Created |
| Initial temporary passwords | Implemented |
| First-sign-in password change requirement | Configured |
| Olivia's fresh temporary password reset | Completed |
| Olivia's Autopilot organisational sign-in | Core onboarding successful |
| Self-Service Password Reset | Deferred |
| Organisation-specific MFA rollout | Pending |
| Conditional Access enforcement | Pending |

This matters because authentication is not a single configuration.

A user having a password does not mean the account is fully protected.

Likewise, having Microsoft Entra ID P2 capabilities available does not mean all our authentication security controls are already operational.

We have implemented the initial credential process.

The stronger protection model still needs to be established and tested.

### From Passwords to Stronger Authentication

Our initial authentication process is now demonstrated.

We have created employee identities, established temporary credentials, configured first-sign-in password changes, and successfully used Olivia's account during the first Windows Autopilot pilot.

But passwords alone are not enough for the security model we intend to operate.

A compromised password could still allow someone to attempt to authenticate as a legitimate employee.

That is why our IAM design includes stronger authentication methods, multi-factor authentication, and risk-aware access controls.

These controls will need to be introduced deliberately, with appropriate testing before wider enforcement.

**Next: Multi-Factor Authentication — moving beyond passwords to stronger verification of Wire Finance identities.**