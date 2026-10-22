---
layout: lesson
title: "Logging and Monitoring"
series: wire-finance
parent: entra-id-implementation
order: 8
description: "Learn where Wire Finance identity changes and sign-in attempts can be investigated in Microsoft Entra, and distinguish native audit evidence from the Defender and Sentinel monitoring still to come."
---

We have created security groups, added our eight pilot employees, checked their memberships and licences, and prepared Olivia Carter's temporary password for her first sign-in.

That leaves an important question: **if someone changes an identity or a security group tomorrow, how would we know what happened?**

The answer starts with logs. But I want to keep the evidence honest: **we have carried out those administrative changes; we have not yet completed a documented, event-by-event audit-log review or connected these logs to a SOC platform.** This lesson introduces the built-in places to investigate them and the checks we'll use as we continue.

### 1. Two log types, two different questions

Microsoft Entra provides several activity reports. For our current stage, two matter most:

| Log | Question it helps answer | Wire Finance example |
|---|---|---|
| **Audit logs** | *Who changed something in the directory, and what changed?* | An administrator created `SG-PILOT-IDENTITY` or updated Olivia's account |
| **Sign-in logs** | *Who attempted to authenticate, to what application, and with what result?* | Olivia's future organizational sign-in during Windows setup |

A third category, **provisioning logs**, tracks supported automated provisioning services. It is **not** a substitute for audit logs when we manually create employees and groups.

In other words:

```text
Directory configuration changes  →  Audit logs
Authentication attempts          →  Sign-in logs
Centralized SOC investigation    →  Future integration
```

These are useful investigation records, not proof that our Conditional Access, Defender or Sentinel controls are already operational.

### 2. Where to find Entra audit logs

Open the [Microsoft Entra admin center](https://entra.microsoft.com/) using an account authorized to read the reports. Microsoft's documented least-privileged directory role for access to these activity logs is **Reports Reader**.

Follow:

**Entra ID → Monitoring & health → Audit logs**

Depending on the portal navigation currently displayed, you may first select **Identity** or **Entra ID**. The important destination is **Audit logs** under **Monitoring & health**.

The practical investigation is read-only:

1. Set the **Date** filter to cover the period when you made the change.
2. Narrow the results using the available **Service**, **Category**, **Activity**, or **Target** filters. Available filters and activity names can vary.
3. Open a relevant event and review its **activity**, **status**, **initiated by**, **target resources**, and any recorded **modified properties**.
4. Record the event time, actor, target, result and correlation or activity identifier where provided.

For our own lab, these are useful events **to look for**, not entries we have already captured from the log viewer:

| Change already performed in the lab | What to investigate in Audit logs |
|---|---|
| Original security-group creation | Creation activity, actor, group name and timestamp |
| Eight pilot accounts created | User-creation activity and target identity |
| Employee properties populated | Attribute-change events where captured |
| `SG-PILOT-IDENTITY` membership assigned | Group membership changes and their initiator |
| `SG-LICENCE-M365-E5` created and populated | Licensing-group creation and membership activity |
| Olivia's password administratively reset | Related password-management audit event, if available within the log window |

**Important:** the fact that a change was completed in the admin center does not mean I have personally opened and verified the corresponding audit event yet. We will only describe an exact event, actor or timestamp as *observed* after checking it in the portal.

Microsoft reference: [What are Microsoft Entra audit logs?](https://learn.microsoft.com/en-us/entra/identity/monitoring-health/concept-audit-logs).

### 3. Sign-in logs: the evidence we'll need for Olivia

The navigation for authentication events is:

**Entra ID → Monitoring & health → Sign-in logs**

Once an employee has attempted to sign in, we can filter by the **user**, **date**, **application** or **status**, open the relevant entry, and inspect the recorded result. When available, the event details help us examine authentication requirements, Conditional Access evaluation, failure reasons and other context.

A failed sign-in deserves more than a red status icon. We should check the application, time, error code, failure reason and correlation ID before deciding what caused it.

At **this point in the website journey**, we have **not yet built the first Windows VM or reached Olivia's Autopilot sign-in**. That means we have no first-device sign-in result to present here.

Later, when Olivia signs in and configures Microsoft Authenticator during the deployment walkthrough, we can examine those actual records. That is also where we'll distinguish **method registration** from **MFA being required and satisfied**; a successful sign-in alone doesn't establish that a Wire Finance MFA policy was enforced.

Microsoft reference: [Troubleshoot sign-in errors](https://learn.microsoft.com/en-us/entra/identity/monitoring-health/howto-troubleshoot-sign-in-errors).

### 4. Native retention is not long-term monitoring

Our Microsoft 365 E5 licensing includes **Microsoft Entra ID P2**. Microsoft currently documents a native retention period of **30 days** for both Entra audit logs and sign-in logs with P1/P2.

That is enough for short-term checks, but it is not a long-term evidence-retention strategy. If we wait too long to investigate an event, it may no longer be available through the native report.

For longer-term retention, Microsoft supports routing selected Entra logs through **Diagnostic settings** to destinations such as an Azure Storage account or a Log Analytics workspace. The configuration begins at:

**Entra ID → Monitoring & health → Diagnostic settings**

**We have not configured that export in this part of the lab.** It needs an Azure destination, appropriate administrator permissions and a cost/retention decision. Because Wire Finance has a constrained Azure lab budget, we'll plan and validate those choices before enabling continuous ingestion.

References: [Microsoft Entra data retention](https://learn.microsoft.com/en-us/entra/identity/monitoring-health/reference-reports-data-retention) and [Configure diagnostic settings](https://learn.microsoft.com/en-us/entra/identity/monitoring-health/howto-configure-diagnostic-settings).

### 5. Where Intune and SOC monitoring will fit

Identity changes are only part of our security story. As we progress into endpoint implementation, Intune will add a separate audit trail for management actions such as creating policies, changing assignments and certain remote device operations.

The documented Intune route is:

**[Intune admin center](https://intune.microsoft.com/) → Tenant administration → Audit logs**

That is **Intune audit logging**, not the same screen as the Microsoft Entra directory audit log. We'll use it alongside the actual Intune RBAC, enrollment and application steps in Chapter 8.

We have **not yet** completed central collection into Microsoft Sentinel, operationalized Microsoft Defender XDR monitoring, deployed detection rules or tested alerts and incident response for this lab. Those belong in later SOC implementation lessons, where we will verify the connectors, ingested data, cost and actual detection results.

Microsoft reference: [Use audit logs in Microsoft Intune](https://learn.microsoft.com/en-us/intune/governance/monitor-audit-logs).

### Our checkpoint

The identity implementation has already produced meaningful administrative activity: security groups, employee accounts, memberships, licences and a password reset. We now know **where to investigate that activity** and what evidence a useful review should collect.

What we **haven't** claimed is equally important: no completed audit-event validation, no first Autopilot sign-in evidence, no configured long-term log export, and no Sentinel or Defender monitoring rollout.

That finishes the initial Entra identity implementation foundation. In the next chapter we'll turn to **endpoint management**, starting with its Intune and enrollment prerequisites before we build and deploy Olivia's first corporate Windows device. The authentication and device events generated during that deployment will then give us real evidence to investigate.