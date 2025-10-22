---
title: "Managing PIM-enabled groups with Entra ID Governance Access Packages just got better!"
date: 2025-07-14
categories: 
  - "entra"
image: "/assests/images/pexels-yankrukov-8197499-1.jpg"
---

Just a quick heads-up for those working a lot with Entra ID Governance: **Access Packages now supports eligible membership and ownership of PIM-enabled groups.** This might sound a bit confusing, as many moving parts and features are involved. Let me explain the new improvement.

[PIM for Groups](https://learn.microsoft.com/en-us/entra/id-governance/privileged-identity-management/concept-pim-for-groups) is excellent for just-in-time ownership or membership for pretty much anything in Entra ID, Microsoft 365, Azure, Intune, or other integrated applications. When a security group becomes PIM-enabled, you can now leverage all the features from Entra ID Privileged Identity Management, like approvals, MFA-enforcement, and integration with Authentication Context in Conditional Access.

![](/assets/images/image-18.png)

![](/assets/images/explorer_7uBSad70Ar.png)

Any assignment of a PIM-enabled group can be active or eligible, both for the member and the owner.

![](/assets/images/image-17.png)

## Support for Access Packages (preview)

Admins can now use Entra ID Governance Access packages to support eligible assignments for PIM-enabled groups. Earlier, only active roles were supported, which did not always fit the requirement of just-in-time access.

![](/assets/images/image-19.png)

![](/assets/images/explorer_weSzC9xDFD-scaled.png)

## User experience

When the user has requested the access package, the user will become eligible to become a member of the group, eventually giving access to the group when needed. You can compare it to receiving a ticket for a concert. You can enter the concert using the ticket, but you still need to pass security (approval, MFA etc.).

![](/assets/images/image-20-scaled.png)

So the user can now go into Privileged Identity Management and become a member of the group whenever needed. If configured, additional steps need to be taken beforehand, like MFA, approval of satisfying Conditional Access policies.

![](/assets/images/image-22-scaled.png)

The user is then automatically removed from the group when the end time is reached. The expiration of the access package itself will remove the active assignment (365 days by default). During an active assignment, the user can become a member of the group whenever needed.

![](/assets/images/image-24-scaled.png)

A welcome enhancement to further expand just-in-time access for your resources.

Stay safe!
