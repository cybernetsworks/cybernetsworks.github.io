---
layout: lesson
title: "Endpoint and Device Management Implementation"
chapter: 8
series: wire-finance
author: Cybernetswork
show_on_master: true
nav_id: endpoint-device-management-implementation
children_heading: "Explore the Endpoint Implementation"
description: "Moving from device design to deployment as we build, register, provision, and validate the first Wire Finance Windows endpoint."
---

Our identity foundation is taking shape.

We have created the first Wire Finance employees, established their group memberships, configured licensing, and introduced our initial authentication process.

But employees need more than an identity.

They need somewhere to work.

Earlier in our Device Identity and Provisioning Model, we defined how Wire Finance endpoints should be owned, named, classified, provisioned, managed, and secured.

Now it is time to put those decisions to work.

**We're going to build our first corporate Windows endpoint.**

## From Device Design to Deployment

Our original device model established three important platform responsibilities.

**Microsoft Entra ID** provides the device identity.

**Microsoft Intune** provides the management platform.

**Windows Autopilot** provides the corporate provisioning experience.

These technologies work together, but they do not perform the same job.

For Wire Finance, our intended provisioning journey looks like this:

```text
Windows 11 Corporate Endpoint
            ↓
Windows Autopilot Registration
            ↓
Corporate Device Classification
            ↓
Autopilot Deployment Profile
            ↓
Employee Organisational Sign-In
            ↓
Microsoft Entra Join
            ↓
Microsoft Intune Enrollment
            ↓
Required Apps and Configuration
            ↓
Device Validation
```

The objective is to move away from manually preparing every employee workstation.

Instead, we want a repeatable process that allows an approved corporate endpoint to receive its organisational identity and management configuration during onboarding.

And we need to test whether that process actually works.

## Building Our First Pilot Endpoint

For the initial implementation, we selected Olivia Carter, our Finance Analyst.

Her account already exists in Microsoft Entra ID.

Her organisational attributes, pilot membership, and Microsoft 365 E5 licensing have also been configured.

Now she needs a corporate workstation.

Rather than purchasing a physical computer for our first deployment, we will use a local **Oracle VirtualBox virtual machine running Windows 11 Pro**.

This gives us a controlled environment for testing the provisioning process.

The VM represents our first corporate device asset:

`WF-LT-001`

This is the intended asset identifier from our device naming convention.

The actual Windows computer name will be verified during deployment rather than assumed to match the asset identifier.

## What We'll Implement

This chapter will follow the technical journey from an empty virtual machine to a managed corporate endpoint.

We'll begin by documenting the Windows 11 VM build, including the configuration decisions needed to preserve the native Windows Out-of-Box Experience.

Next, we'll configure the Microsoft environment to support automatic enrollment and Windows Autopilot provisioning.

That includes:

- Automatic MDM enrollment
- Autopilot device registration
- Corporate device classification
- Dynamic device-group membership
- Enrollment Status Page configuration
- Autopilot deployment profile
- Required application assignments

Once those components are ready, we'll follow Olivia through the Windows onboarding process.

And importantly, we won't skip the parts that didn't work on the first attempt.

## Troubleshooting Is Part of the Implementation

A working endpoint is the result we want.

But getting there is part of the learning.

During the implementation, we encountered challenges involving PowerShell execution policy, authentication, Microsoft Graph permissions, portal navigation, and configuration naming.

These are not details to hide.

They're useful opportunities to understand how the different components interact and why particular decisions matter.

Where something failed, we'll examine what happened, how we investigated it, and what change allowed us to continue.

**The goal is not to produce a collection of perfect screenshots. It is to document a deployment that someone else can understand and reproduce.**

## Beyond the First Workstation

Completing Windows onboarding is an important milestone.

But a device reaching the desktop does not automatically mean it meets our corporate security requirements.

We still need to validate the resulting device identity, management state, application delivery, and security posture.

Our later implementation work will introduce endpoint protection, device compliance, and identity-based access controls.

Once those controls are properly configured and tested, we can expand the pilot to additional employees representing different departments.

That will allow us to test whether the provisioning model works beyond a single workstation.

## The First Step

Before we register anything with Windows Autopilot, we need a Windows device to register.

That means creating the virtual machine, installing Windows 11, and preparing the environment for corporate provisioning.

We'll begin with the foundation of our first endpoint.

**Next: Building the First Windows 11 Virtual Machine — preparing Olivia Carter's pilot workstation in Oracle VirtualBox.**