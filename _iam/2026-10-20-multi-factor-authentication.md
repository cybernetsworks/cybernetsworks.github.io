---
layout: lesson
title: "Multi-Factor Authentication"
series: wire-finance
parent: entra-id-implementation
order: 7
description: "Exploring multi-factor authentication in Wire Finance, from Olivia's first Microsoft Authenticator experience to the planned organisation-wide security controls."
---

Our first eight employees now have Microsoft Entra identities, organisational attributes, and an initial authentication process.

We have also established how temporary passwords are generated and how employees are required to change them during their first sign-in.

But that introduces another security question.

**What happens if somebody obtains an employee's password?**

A password proves that somebody knows a credential.

It doesn't necessarily prove that the person signing in is the legitimate employee.

This is where Multi-Factor Authentication (MFA) becomes important.

## Why MFA Matters

Multi-Factor Authentication strengthens identity verification by requiring more than one authentication factor.

These factors generally include:

- Something you know, such as a password.
- Something you have, such as a registered authenticator or security key.
- Something you are, such as a biometric characteristic.

By combining independent factors, MFA reduces the likelihood that a stolen password alone will be enough to access an account.

For Wire Finance, this matters because employees will eventually access business applications, financial information, managed endpoints, and administrative systems.

However, **having MFA available, experiencing an MFA challenge, and enforcing an organisation-wide MFA policy are not the same thing.**

That distinction is important to our implementation.

## Our First Microsoft Authenticator Experience

During Olivia Carter's first Windows Autopilot pilot, we encountered an additional authentication requirement.

Olivia was required to use the **Microsoft Authenticator app** as part of her organisational sign-in.

This was a real part of the pilot experience.

However, we had not yet implemented our own organisation-wide Conditional Access policy to enforce MFA.

That raises an interesting question.

**Why was Microsoft Authenticator required when our custom MFA rollout had not yet been configured?**

Microsoft Entra environments can introduce authentication requirements through different mechanisms, including existing tenant protections, authentication registration requirements, and Conditional Access policies.

We therefore need to distinguish the authentication behaviour we observed from the policy configuration responsible for it.

The exact mechanism responsible for Olivia's challenge still needs to be established through the tenant's authentication settings and available sign-in records.

When we reach the Windows Autopilot implementation, we'll document Olivia's actual sign-in experience, including the Microsoft Authenticator step.

For now, the important observation is that additional authentication formed part of our first endpoint onboarding experience.

## MFA Capability vs Policy Enforcement

Wire Finance uses Microsoft 365 E5, which includes Microsoft Entra ID P2 capabilities.

This gives us the licensing foundation for advanced authentication and Conditional Access.

Our current implementation position is:

| Component | Implementation Status |
|---|---|
| Microsoft Entra ID P2 | Available and verified |
| Initial password authentication | Implemented |
| First-sign-in password change | Configured |
| Microsoft Authenticator during Olivia's pilot | Observed |
| Organisation-specific MFA policy | Not yet implemented |
| Conditional Access enforcement | Pending |
| Self-Service Password Reset | Deferred |
| Privileged identity MFA requirements | Pending |

This means we have already encountered MFA-related behaviour during the pilot, but our planned organisation-wide authentication model still requires implementation.

We should not assume that one successful Authenticator interaction proves every employee is protected by the same requirements.

## Preparing for Controlled MFA Deployment

Before introducing our own enforcement policies, we need to review the authentication environment.

This includes:

1. Identifying the tenant's current Security Defaults configuration.
2. Reviewing available and enabled authentication methods.
3. Checking authentication-method registration for our pilot identities.
4. Reviewing existing Conditional Access policies, if any.
5. Preparing privileged and emergency-access arrangements.
6. Defining the intended MFA requirements for standard and privileged identities.

We already have a useful pilot population:

`SG-PILOT-IDENTITY`

This group gives us a controlled set of employees for evaluating authentication changes.

However, the final targeting, exclusions, and authentication requirements must be established before enforcement.

## Our Planned MFA Rollout

We intend to introduce MFA through a controlled implementation process.

```text
Review Existing Authentication
          ↓
Define Approved Methods
          ↓
Prepare Pilot Identities
          ↓
Configure Authentication Policies
          ↓
Evaluate Conditional Access
          ↓
Review Report-Only Results
          ↓
Enable Controlled Enforcement
          ↓
Validate Actual Sign-Ins
          ↓
Expand Approved Coverage
```

Report-only evaluation will help us understand how policies would affect users before enforcement.

However, report-only results cannot prove that MFA was actually enforced.

That requires controlled authentication testing after the approved policy is enabled.

When we reach this stage, we'll document the actual configuration, including the selected methods, policy conditions, grant controls, targeting groups, exclusions, and observed results.

## What Will Count as Successful MFA Implementation?

For Wire Finance, simply enabling a feature in Microsoft Entra will not be enough.

We need to confirm that:

- The intended employees are correctly targeted.
- The approved authentication methods are available and registered.
- MFA is required in the expected scenarios.
- Legitimate employees can complete authentication successfully.
- Unintended authentication behaviour is investigated.
- Administrative recovery arrangements remain functional.
- Sign-in records support the results we observe.

Relevant screenshots and validation evidence will accompany those configurations when they are implemented.

This will allow readers to reproduce the configuration and understand how we determined whether it worked.

## From Identity Authentication to Device Onboarding

We have now established the identity foundation for Wire Finance.

Our initial employees exist in Microsoft Entra ID.

Their organisational attributes and group memberships are configured.

We have implemented the initial password-provisioning process and encountered Microsoft Authenticator during our first pilot onboarding experience.

But identities are only one part of the environment we designed.

Earlier, we established that Wire Finance would use corporate-owned Windows endpoints, Microsoft Entra Join, Microsoft Intune, and Windows Autopilot.

Now we need to bring that design into the real environment.

Rather than simply giving Olivia Carter a username and password, we want to provide a workstation that can recognise the organisation, establish its device identity, enrol into management, and receive the required configurations.

And because this is a learning journey, we'll document the entire process.

From building the Windows 11 virtual machine to resolving the challenges encountered during Autopilot registration and completing the employee onboarding experience.

**Next: Endpoint and Device Management Implementation — building our first Windows 11 corporate workstation and preparing it for Microsoft Autopilot.**