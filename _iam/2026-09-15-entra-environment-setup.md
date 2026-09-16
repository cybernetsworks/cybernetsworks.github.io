---
layout: lesson
title: "Entra Environment Setup"
series: wire-finance
parent: entra-id-implementation
order: 1
---

Before we create the first Wire Finance user, group, or administrative role, we need somewhere to build them.

The IAM design tells us what the environment **should** look like.

Now we need the Microsoft environment that will make that design possible.

For Wire Finance, the starting point was setting up the Microsoft tenant and obtaining the licensing required for the identity and security capabilities we plan to use.

#### Setting Up the Microsoft Environment

I began through Microsoft Partner Center using the Value Added Reseller enrolment path:

[Microsoft Partner Center enrolment](https://partner.microsoft.com/en-us/dashboard/account/exp/enrollment/welcome?cloudInstance=Global&accountProgram=valueaddedreseller)

The purpose here was not simply to create another Microsoft account.

Wire Finance needs its own organisational environment where we can safely build identities, groups, authentication controls, administrative roles, endpoints, and eventually the wider security stack without mixing the project with a personal environment.

Once the enrolment and tenant setup were complete, we had the foundation on which the rest of the project could be built.

At this point, however, having a tenant was only part of the requirement.

We also needed to make sure the environment had access to the identity and security capabilities required by the Wire Finance design.

#### Adding the E5 Licence

With the tenant available, the next requirement was licensing.

For the Wire Finance environment, I obtained a **Microsoft 365 E5 licence** through the Microsoft 365 environment (Microsoft 365 admin center).

The exact location of these options inside Microsoft's portals may change over time, so the important part of this stage is not memorising a particular sequence of buttons.

The important outcome is:

```text
Microsoft Tenant
        +
Active Microsoft 365 E5 Licence
        ↓
Identity and Security Capabilities Available
```

After obtaining the licence, I verified that the Microsoft 365 E5 subscription appeared as active in the tenant.

![Microsoft 365 E5 subscription]({{ '/assets/images/active-directory/e5-active-subscription.png' | relative_url }})

For the identity side of the project, Microsoft 365 E5 gives us access to Microsoft Entra capabilities that will become important as the environment develops.

These include areas such as:

- Conditional Access
- Identity Protection
- Risk-based access controls
- Privileged Identity Management
- Advanced identity security capabilities

We are not configuring those features yet.

At this stage, the goal is simply to make sure the environment can support the controls we intend to introduce later.

#### Licensing the Administrative Account

During the initial tenant setup, an administrative account was created to manage the environment.

I assigned the appropriate licence to this account so that it could work with the licensed capabilities required during the early stages of the lab.

![Administrative account licence assignment]({{ '/assets/images/active-directory/admin-licence-assignment.png' | relative_url }})

This account currently provides the administrative access needed to begin configuring the environment.

That does not mean we intend to rely on broad administrative access permanently.

Earlier in the IAM design, we established principles around:

- Separate privileged identities
- Least privilege
- Controlled administrative access
- Avoiding unnecessary Global Administrator usage

Those controls will become increasingly important as the implementation develops.

For now, the existing administrative account gives us a controlled starting point from which we can begin building the environment.

Later, we will replace broad access with the more deliberate privileged-access model already defined for Wire Finance.

#### Why Licensing Comes Before Configuration

It would be easy to jump straight into the Microsoft Entra admin centre and start creating users.

But licensing affects which capabilities are available inside the environment.

Our IAM design already includes concepts such as:

- Strong authentication
- Privileged access management
- Access controls
- Identity risk
- Security monitoring

If the environment cannot support those capabilities, we would eventually reach a point where the implementation no longer matches the design.

So before creating anything, we want to answer a few basic questions:

```text
Do we have a working tenant?
        ↓
Do we have the required licence?
        ↓
Are the identity capabilities we need available?
        ↓
Then we begin implementation
```

That gives us a much cleaner starting point.

## A Note About the Microsoft Portals

Microsoft regularly updates the layout and navigation of its administrative portals.

A menu available in one location today may appear somewhere else later.

Because of that, this project will focus less on memorising individual button locations and more on understanding:

- What we are trying to configure
- Why the configuration is required
- What the expected result should be
- How that result connects back to the Wire Finance design

Screenshots will still show what the environment looked like while the project was being built, but the important part is understanding the configuration rather than reproducing an exact sequence of clicks.

#### The Environment Is Ready

At this stage, Wire Finance has the foundation required to begin the identity implementation:

```text
Microsoft Tenant
        +
Microsoft 365 E5
        ↓
Wire Finance Identity Environment
```

We have not created our workforce yet.

We have not created the department and role groups.

We have not assigned the administrative roles from our design.

We have not configured authentication controls.

And we have not started building the monitoring capabilities that will eventually feed into security operations.

That is intentional.

The environment exists, but the Wire Finance identity model still needs to be built inside it.

Up to this point, we have been preparing the ground.

Now we can create the first identities.

And because the IAM design already tells us who those users are, what naming convention they should follow, and what type of access they should eventually receive, we are not starting from a blank page.

**The next question is: how do we turn the Wire Finance workforce we designed earlier into real Microsoft Entra identities?**