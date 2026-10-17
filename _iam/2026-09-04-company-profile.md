---
layout: lesson
title: "Company Profile"
chapter: 3
series: wire-finance
show_on_master: true
author: Cybernetswork
image: assets/images/active-directory/profile.png
description: Meet the people, departments, technologies, and operating model that will shape every security decision inside Wire Finance.
---

Before we decide how Wire Finance should manage identities, secure devices, or detect threats, we first need to understand the organisation we are protecting.

Security does not exist in isolation.

The number of employees, the departments they work in, where they work from, the systems they use, and even the size of the security team will influence the controls we eventually put in place.

Wire Finance is intentionally small enough for us to understand its environment clearly, while still having enough moving parts to introduce realistic identity and security challenges.

### Wire Finance at a Glance

| Area | Wire Finance |
|---|---|
| **Industry** | Professional and financial services |
| **Employees** | 25 |
| **Locations** | One office plus remote workers |
| **Departments** | Finance, HR, Operations, Sales, and IT |
| **Cloud Platform** | Microsoft 365 and Azure |
| **Identity** | Microsoft Entra ID initially |
| **Endpoint Management** | Microsoft Intune |
| **Endpoint Security** | Microsoft Defender for Endpoint |
| **Email Security** | Microsoft Defender for Office 365 |
| **SIEM / SOAR** | Microsoft Sentinel |
| **Future Expansion** | On-premises Active Directory and hybrid identity |
| **SOC Model** | Small internal security operations capability |
| **Azure Budget** | Maximum of $150 |
| **Lab Compute Model** | Most virtual machines run locally, with Azure used only where required |

### Departments

Wire Finance is divided into five departments:

| Department | Employees | Business Function |
|---|---:|---|
| **Finance** | 5 | Financial records, reporting, accounting, and payment-related activity |
| **HR** | 2 | Employee information, recruitment, onboarding, and people operations |
| **Operations** | 7 | Day-to-day business processes and customer operations |
| **Sales** | 7 | Customer relationships, account activity, and revenue generation |
| **IT** | 4 | Technology administration, endpoint deployment, identity, and security operations |
| **Total** | **25** | |

Although every employee works for the same company, their responsibilities are different. Those differences will later influence the access, applications, devices, and security controls they receive.

### People We Will Follow

Rather than treat Wire Finance as a collection of anonymous accounts, we will follow several employees throughout the project.

| Employee | Department | Role |
|---|---|---|
| **Jordan Lee** | IT | Head of Security Operations |
| **Alex Morgan** | IT | IT Administrator |
| **Taylor Reed** | IT | SOC Analyst |
| **Casey Ng** | IT | Deployment/Support Engineer |
| **Maya Patel** | HR | HR Manager |
| **Olivia Carter** | Finance | Finance Analyst |
| **Ethan Brooks** | Operations | Operations Specialist |
| **Noah Williams** | Sales | Sales Representative |

These identities will eventually help us test real scenarios such as employee onboarding, role-based access, privileged administration, endpoint deployment, security monitoring, and incident investigation.

### Technology and Operating Model

Wire Finance begins as a cloud-first organisation.

Microsoft Entra ID provides the initial identity foundation, while Microsoft Intune manages corporate endpoints. Microsoft Defender will provide endpoint and email protection, and Microsoft Sentinel will later bring security telemetry together for monitoring, detection, and response.

The environment will not remain cloud-only forever.

As the project develops, Wire Finance will introduce on-premises Active Directory and hybrid identity so that we can explore how modern cloud services interact with traditional enterprise infrastructure.

The lab also has practical constraints.

Most virtual machines will run locally, while Azure will be used only where cloud infrastructure is genuinely required. This keeps the environment realistic while controlling unnecessary cloud spend.


### Why the Company Profile Matters

A 25-person company may sound simple, but even this environment already contains different responsibilities, sensitive information, remote access requirements, administrative roles, and security obligations.

Finance should not automatically have the same access as HR.

A Finance Analyst may need financial information without automatically receiving authority to process payments.

IT staff may require elevated administrative capabilities, but those privileges should be governed separately from their everyday employee access.

Remote workers also introduce another question: knowing who the user is may not be enough. We may also need to understand which device they are using and whether that device meets the organisation's security requirements.

These are not yet access-control decisions.

They are the business conditions that our identity and security architecture must respond to.

### A Small Organisation with Real Security Problems

Wire Finance may be fictional, but the problems we are going to solve are not.

People will join the organisation, move between roles, receive new responsibilities, work remotely, require temporary access, gain administrative privileges, and eventually leave.

Devices will be purchased, provisioned, reassigned, secured, and retired.

Cloud services will generate logs and alerts.

And eventually, something will go wrong.

Before any of that happens, however, Wire Finance needs an identity model capable of answering one fundamental question:

**Who should have access to what — and why?**

That is where our Identity and Access Management design begins.










