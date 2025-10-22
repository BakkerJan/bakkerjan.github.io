---
title: "Multi-stage approval for privileged roles using Azure AD Identity Governance"
date: 2022-05-15
categories: 
  - "entra"
  - "security"
image: "/assets/images/pexels-pixabay-272267-scaled.jpg"
---

Privileged Identity Management is a great feature within Azure AD to provide just-in-time access to your admin roles and Azure resources. But some roles may require an extra level of security, for the simple fact the role is highly privileged, and (hopefully) rarely used.

With the current capabilities, you are able to activate an approval step, but that is only limited to **one** approver. In this article, I will show you how you can use Azure AD Identity Governance to create a multi-stage approval process to grant access to admin roles. Also, this shows how Identity Governance features are intertwined to meet customers' requirements for flexibility.

Let's take a look at the overview first.

![](/assets/images/image-4.png)

We are using a **Role Assignable Group** that has standing access to the **Global Administrator role**. That group is onboarded into Privileged Identity Management, so converted to a **Privileged access group**. Using an **Access Package** we grant the user time-based access to the group. The access package can be available to a static group of users, or even dynamic. The user flow is as follows:

1. User requests access package.
2. Multi-stage approval flow kicks in.
3. After the request is approved, the user is added as a member of the Role Assignable Group.
4. Because the user is now a member of the group, the user is elevated to Global Administrator.
5. After the access package expires, the user is deleted from the group, and the admin role is no longer active.

Let me show you how to build this step by step.

## Create a Role Assignable Group

First, let's create a new group, and make sure that roles can be assigned to the group. Do not add any owners, members, or roles yet.

Role-assignable groups are designed to help prevent potential breaches by an extra layer of security. [How are role-assignable groups protected?](https://docs.microsoft.com/en-us/azure/active-directory/roles/groups-concept#how-are-role-assignable-groups-protected)

![](/assets/images/image-5.png)

After the group is created, enable **_privileged access_** to the group. The group is now onboarded to Privileged Identity Management so we can grant the members and owners just-in-time access.

![](/assets/images/image-6.png)

## Grant the Global Administrator role to the group

Next, we need to add the Global Administrator role to the group. Open the group, and choose **Assigned roles** -> **+ Add assignments**

![](/assets/images/image-9.png)

Make sure you pick the right role and choose the **active** assignment.

![](/assets/images/image-8.png)

![](/assets/images/msedge_RcyQx2ycXy-729x1024.png)

## Create a group for the users that can request the access package

Next, we will also create a _regular_ security group for the users that can request the access package. This can be either a static or dynamic group. I created a **_dynamic_** group for my Level 3 IT support engineers.

![](/assets/images/image-7.png)

## Building the access package

Head over to the Identity Governance feature in Azure AD, and select Access packages from the left-hand menu. Create a new Access Package.

![](/assets/images/image-10.png)

Now, there is a lot that you can configure in an access package, but for the purpose of this demo, we focus on the multi-stage approval. Next, you can configure the settings to your own needs. In this short video, you will see a walkthrough through all the steps. In short:

The package will contain the global admin group, with the **_member_** role. The package will be available to the dynamic IT group. Next, the package will expire in 4 hours, so the access is revoked. Approval is enabled, and after the manager approved, a second approver needs to approve the request before the access is granted. You can request additional information like the ticket/change number.

![](/assets/images/msedge_20HVrDmdFk.gif)

Here's an overview of all the settings in this demo:

![](/assets/images/CkMsUWk03u.png)

Additionally, you can also **_hide_** the access package. If hidden, this access package can only be discovered and requested using its direct URL found on the entitlement's overview page. It will not show up in a user's My Access portal under the list of access packages they can request.

![](/assets/images/image-11.png)

## End-user experience

Now that we have all the bits in place, let's see what this looks like from an end-user perspective.

An overview of all the available access packages can be found on the [My Access portal](https://myaccess.microsoft.com/). Here, you can see the contents of the package, and request it.

![](/assets/images/image-12.png)

The user needs to add justification and the extra requestor information that is configured.

![](/assets/images/image-13.png)

The status of the request can be found here:

![](/assets/images/image-14.png)

Alex's manager is Miriam. She will receive an email to approve the request. Also, the request can be found in the My Access Portal.

![](/assets/images/msedge_Jvv5MLjF7q-1024x607.png)

![](/assets/images/image-15.png)

Now the approval is going to the second stage. In this case, Bianca is the second approver. She will also receive an email and can see the approval history of the request. After she approves, the request will continue to the next stage: **delivery**.

![](/assets/images/image-17.png)

From an admin experience, the requests can also be tracked. Here we can see the entire flow, and we can see that the request is being delivered. Alex is now added to the global admin group.

![](/assets/images/image-19.png)

On the user object, we can see that the role is now active and that it is granted through the global admin group.

![](/assets/images/image-18.png)

After the package expires, the role is revoked, and Alex will no longer be Global Admin.

## Some considerations

Now, as you have seen you can use this method to grant access to privileged roles with a multi-stage approval flow. One thing that is missing in this flow is **_Azure MFA_**. Although we can require MFA for members of the privilege access group, this setting is ignored when we use an access package.

A way to solve this is to change the active Global Administrator role to **eligible**. When the request is approved, the users then need to activate the role through Privileged Identity Management, and all the additional settings will apply, including MFA. Here is an overview of that scenario.

![](/assets/images/MicrosoftWhiteboard_lOdGsCExDP.png)

## Final thoughts

As you can see, there are a lot of use-cases that can be solved using the Identity Governance features in Azure AD. The features are so heavily intertwined and therefore bring great flexibility. Now, all of this goodness requires an Azure AD Premium P2 license, for both the requesters and the approvers. [Read more](https://docs.microsoft.com/en-us/azure/active-directory/governance/entitlement-management-overview#license-requirements).

If you want to learn more about Identity Governance, please check out the swarm that is going on a the moment, with lots of great content around this topic. [Make Azure AD Identity Governance work for you! - Microsoft Tech Community](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/make-azure-ad-identity-governance-work-for-you/ba-p/2810643)

Stay safe!
