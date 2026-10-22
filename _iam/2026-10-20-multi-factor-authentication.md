---
layout: lesson
title: "Multifactor Authentication (MFA)"
series: wire-finance
parent: entra-id-implementation
order: 7
description: "Explain Wire Finance's MFA approach, distinguish method registration from enforcement, and prepare for Microsoft Authenticator during the first employee-device sign-in."
---

We've created the Wire Finance security groups, added our eight pilot employees and prepared Olivia Carter's temporary password for her first sign-in.

But if someone steals that password, what stops them from accessing company resources?

**Multifactor authentication (MFA) is part of our security design. It isn't yet a fully implemented and validated Wire Finance control at this point in the learning journey.** That's an important distinction: we have the licensing and identities to begin testing, but we're not going to pretend we completed a policy we haven't configured.

### 1. What we're trying to achieve

A password is something the employee **knows**. MFA adds another form of verification, such as an approved authenticator on a device they **have**.

Our intended sign-in model is:

```text
Employee signs in with work credentials
                 ↓
Microsoft Entra evaluates the sign-in
                 ↓
MFA is requested when policy requires it
                 ↓
Verification succeeds or fails
                 ↓
Access decision
```

For Wire Finance, the longer-term goal is to require strong verification for sensitive access, especially privileged administration, and to roll out workforce MFA through controlled testing rather than switching on broad policies without knowing their impact.

### 2. What we've actually configured so far

| Area | Status at this chapter |
|---|---|
| Microsoft 365 E5 / Microsoft Entra ID P2 | **Available** in our tenant |
| `SG-PILOT-IDENTITY` | **Created** for our selected pilot employees |
| Eight standard users and their licences | **Implemented** |
| Olivia's temporary password | **Reset successfully**, with change required at first sign-in |
| Olivia's Microsoft Authenticator setup | **Not yet reached** in the website journey |
| Wire Finance authentication-method policy | **Not yet configured and verified as a pilot control** |
| Wire Finance Conditional Access MFA enforcement | **Not implemented or validated** |
| Self-service password reset (SSPR) | **Deferred** |

An existing tenant setting may influence authentication prompts, even when we haven't created our own Conditional Access policy. We haven't recorded the exact state of every tenant-wide protection here, so we won't claim those settings are enabled or disabled without checking.

### 3. Three things we must not confuse

It's tempting to call MFA *done* as soon as a user opens Microsoft Authenticator. But there are three separate milestones:

| Milestone | What it proves |
|---|---|
| **Method allowed** | The user is permitted to use an authentication method under the applicable tenant settings |
| **Method registered** | The user has linked that method to their work identity |
| **MFA required and satisfied** | An actual sign-in was subject to an MFA requirement, and its authentication details show how the requirement was met |

**Registration alone does not establish enforcement.** And a prompt during sign-in doesn't, by itself, prove which policy caused it.

Microsoft separates [authentication-method management](https://learn.microsoft.com/en-us/entra/identity/authentication/concept-authentication-methods-manage) from [Conditional Access](https://learn.microsoft.com/en-us/entra/identity/conditional-access/overview), which determines when additional verification or other access requirements apply.

### 4. Why we're not enforcing MFA across the tenant yet

Our next objective is to prove the first employee-device onboarding path. We also still need dedicated `adm-*` accounts and a tested emergency-access approach before confidently expanding privileged-access controls.

So our **planned** security rollout is:

1. Inspect the existing authentication methods and tenant protection settings rather than assume their state.
2. Complete Olivia's first Windows onboarding and observe any authentication-method registration at its natural point in the sign-in.
3. Verify which methods were registered and examine the actual sign-in logs.
4. Prepare separate privileged and emergency-access controls.
5. Test a carefully scoped Conditional Access MFA policy in **Report-only**, review its impact, then make a deliberate enforcement decision.

Report-only is for evaluation: it does **not** enforce the MFA grant requirement. That lets us analyse potential effects before turning a policy on. Microsoft explains this in [Analyse Conditional Access policy impact](https://learn.microsoft.com/en-us/entra/identity/conditional-access/concept-conditional-access-report-only).

These steps are a **future implementation plan**, not a description of changes already saved in the tenant.

### 5. Where we'll collect evidence later

When we reach MFA implementation and validation, we'll use the Microsoft Entra admin center to check:

| Question | Where to check |
|---|---|
| Which methods are allowed? | **Entra ID → Authentication methods → Policies** |
| What has Olivia registered? | **Entra ID → Users → All users → Olivia Carter → Authentication methods** |
| Which Conditional Access requirements apply? | **Entra ID → Conditional Access** |
| Did a real sign-in satisfy MFA, and how? | **Entra ID → Monitoring & health → Sign-in logs** |

These are **future verification locations**. We'll record the precise settings and screenshots when we actually work through them.

### 6. Microsoft Authenticator belongs with Olivia's first sign-in

We haven't yet built the planned `WF-LT-001` Windows 11 VM in the website walkthrough. That also means we haven't reached Olivia's organizational sign-in on that device.

**We'll document her Microsoft Authenticator registration when it happens during that first sign-in**, alongside the real Windows screens, instructions she follows and any challenges we encounter. We deliberately didn't sign her into a separate browser beforehand just to complete authentication setup early.

There is no Authenticator screenshot on this page because doing so would put the evidence ahead of the actual event in our learning journey.

### Our checkpoint

Our identity and licensing foundation is ready, and Olivia's temporary password has been prepared. **Microsoft Authenticator registration, MFA sign-in verification and Conditional Access enforcement are not completed at this point in the documented sequence.** SSPR remains deferred.

Next, we'll look at **Logging and Monitoring**. The hands-on Authenticator experience will come later, in our first Windows Autopilot deployment walkthrough.