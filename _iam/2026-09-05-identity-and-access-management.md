---
layout: lesson
title: "Identity and Access Management"
chapter: 4
series: wire-finance
show_on_master: true
nav_id: iam-design
children_heading: "Explore the IAM Design"
author: Cybernetswork
image: assets/images/active-directory/profile.png
description: Wire Finance has people, departments, remote workers, cloud services, and sensitive data. Now we need to decide who gets access to what — and why.
---

Wire Finance now has employees, departments, cloud services, remote workers, and sensitive information.

The next challenge is deciding who should have access to what — and ensuring that access remains appropriate as people join, move through the organisation, or take on more privileged responsibilities.

### Design Purpose

Wire Finance needs to understand not only **who an identity is**, but also **what that identity represents, what it should be allowed to access, and how that access should change over time**.

The purpose of the Identity and Access Management design is to ensure that users, administrators, devices, applications, and workloads receive only the access required to perform their authorised functions.

Simply creating accounts and assigning permissions is not enough.

Wire Finance needs an identity model that can grow with the organisation while keeping access controlled, understandable, and auditable.

### Design Principles

Four core principles will guide the IAM design.

#### Least Privilege

Users, administrators, applications, and workloads should receive only the access required to perform their authorised responsibilities — and no more than necessary.

#### Role-Based Access Control

Access should be aligned with job responsibilities rather than assigned randomly to individual users.

#### Separation of Duties

Sensitive or administrative activities should not depend on a single identity having unrestricted control.

#### Zero Trust

Access should not be trusted simply because a user or device is inside the organisation.

Identity, authentication strength, device state, risk, and context should all contribute to access decisions.

These principles will shape every identity decision we make as Wire Finance grows.







