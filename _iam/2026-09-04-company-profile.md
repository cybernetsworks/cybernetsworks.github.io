---
layout: lesson
title: "Company Profile"
chapter: 3
series: wire-finance
author: Cybernetswork
image: assets/images/active-directory/profile.png
description: Meet the people, departments, technologies, and operating model that will shape every security decision inside Wire Finance.
---

Before we decide how Wire Finance should manage identities, secure devices, or detect threats, we first need to understand the organisation we are protecting.

Security does not exist in isolation.

The number of employees, the departments they work in, where they work from, the systems they use, and even the size of the security team will influence the controls we eventually put in place.

Wire Finance is intentionally small enough for us to understand its environment clearly, while still having enough moving parts to introduce realistic identity and security challenges.

#### Wire Finance at a Glance

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
| **SOC Model** | Small internal SOC with one analyst and an IT administrator |
| **Azure Budget** | Maximum of $150 |
| **Lab Model** | Virtual devices with a small cloud footprint |

#### Why Does This Matter?

At first glance, 25 employees might not sound like a particularly complicated environment.

But consider what already exists inside the company.

Finance handles accounting, payments, reporting, and financial records.

HR works with employee information, recruitment, and onboarding.

Operations deals with business processes and customer activity.

Sales manages customer relationships and account information.

IT is responsible for administration, deployment, and security operations.

These teams work for the same organisation, but they should not automatically have access to the same information.

A member of the Sales team should not gain access to confidential HR records simply because they are an employee.

Someone in Finance may require access to financial records without necessarily having authority to process payments.

An IT administrator may need elevated permissions to manage systems, but those permissions should not be attached to the same account they use for everyday email and web browsing.

And with employees working both inside and outside the office, identity becomes one of the key ways Wire Finance determines who is connecting, what they are allowed to access, and under what conditions that access should be trusted.

#### A Small Organisation with Real Security Problems

Wire Finance may be fictional, but the problems we are going to solve are not.

As the organisation grows, people will join, change departments, receive new responsibilities, work remotely, require temporary access, gain administrative privileges, and eventually leave the company.

The technology environment will grow as well.

What begins with Microsoft Entra ID and cloud services will eventually expand into Active Directory and hybrid identity, giving us the opportunity to explore how modern and traditional identity systems coexist.

The challenge is therefore not simply to create user accounts.

It is to create an identity and access model that can answer a much more important question:

**Who should have access to what — and why?**