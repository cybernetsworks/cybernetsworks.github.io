---
layout: lesson
title: "Device Provisioning Model"
chapter: 6
series: wire-finance
show_on_master: true
nav_id: device-identity-provisioning
children_heading: "Explore the Device Provisioning Model"
author: Cybernetswork
image: assets/images/active-directory/device-provisioning.png
description: Before a device can be enrolled, secured, or trusted, Wire Finance needs to decide what kinds of devices exist, who owns them, how they receive an identity, and how they move through the organisation.
---

Wire Finance now has an identity model.

We know who our employees are, what access they should receive, how privileged access is separated, and how those permissions change throughout the employee lifecycle.

But users do not normally access company resources on their own.

They do so through devices.

That introduces a new set of questions.

What kind of device is being used?

Who owns it?

How should it be identified?

How does it become part of Wire Finance?

How should it be provisioned and managed?

What makes it trusted?

And what happens to it when it is reassigned, rebuilt, or retired?

### Why Device Design Comes Next

Knowing that Olivia Carter is a Finance Analyst tells us a great deal about **who** is requesting access.

It does not tell us whether the device she is using should be trusted.

```text
Olivia Carter
      │
      ├── Managed Corporate Laptop
      ├── Privileged Workstation
      ├── Personal Device
      └── Unknown Device
