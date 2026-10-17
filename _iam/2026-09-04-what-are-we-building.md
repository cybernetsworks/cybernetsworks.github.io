---
layout: lesson
title: "What are we Building?"
chapter: 2
series: wire-finance
show_on_master: true
author: Cybernetswork
image: assets/images/active-directory/building.png
description: Discover how Wire Finance will evolve from an identity foundation into a connected security environment spanning endpoints, cloud, monitoring, detection, and incident response.
---

Wire Finance will not be built all at once.

Instead, the environment will grow in stages, with each layer creating the foundation for the next.

The journey begins with identity.

Before we can secure devices, monitor activity, detect threats, or investigate incidents, we first need to understand who our users are, what they should have access to, and what normal behaviour should look like.

### The Build Path

#### 1. Identity and Access

We begin with Microsoft Entra ID and establish the identity foundation for Wire Finance.

This includes:

- workforce identities
- departments and job roles
- group-based access
- privileged identities
- authentication
- Joiner, Mover, and Leaver processes
- access governance

The goal is to define **who should have access to what, and why**.

#### 2. Endpoint and Device Management

Once identities exist, we introduce the devices those identities will use.

Microsoft Intune and Windows Autopilot will help us manage:

- corporate Windows endpoints
- device provisioning
- application deployment
- configuration policies
- endpoint security
- compliance
- device lifecycle management

The goal is to move from simply knowing the user to also understanding whether the device they are using should be trusted.

#### 3. Endpoint and Email Security

Microsoft Defender for Endpoint and Microsoft Defender for Office 365 will provide protection and telemetry across devices and email.

This will allow us to explore areas such as:

- malware protection
- endpoint detection and response
- device risk
- phishing and malicious email
- investigation and response

#### 4. Hybrid Identity and On-Premises Infrastructure

Wire Finance will later expand beyond its cloud-first environment.

We will introduce:

- on-premises Active Directory
- Windows Server
- identity synchronisation
- hybrid identity
- traditional infrastructure
- legacy-system scenarios

This gives us a way to explore how modern cloud security interacts with technology that many organisations still rely on.

#### 5. Monitoring and Detection

Once the environment is generating meaningful security data, Microsoft Sentinel will become the central SIEM platform.

Logs and alerts from across the environment will allow us to explore:

- security monitoring
- analytics rules
- detection engineering
- alert triage
- threat hunting
- automation

#### 6. Security Operations and Incident Response

Finally, we will bring everything together from the perspective of a security operations team.

Instead of looking at technologies in isolation, we will investigate realistic scenarios across identities, endpoints, email, cloud services, and infrastructure.

The goal is to understand not only that something suspicious happened, but:

- who was involved
- which device was used
- what access existed
- what security controls responded
- what evidence was generated
- how the incident should be contained and investigated

#### The Bigger Picture

The project follows a simple progression:

**Design → Implement → Validate → Monitor → Detect → Investigate → Respond**

Each stage builds on the one before it.

By the time Wire Finance reaches its security operations phase, the alerts and incidents we investigate will come from an environment we already understand because we designed and built it ourselves.

In short, we are building a company, securing it, and learning from everything that happens along the way.





