---
title: "Azure Active Directory Identity Governance – Azure AD Entitlement Management"
date: 2020-12-20
categories: 
  - "entra"
  - "security"
tags: 
  - "access-package"
  - "access-review"
  - "approval"
  - "azure-ad"
  - "entitlement"
  - "privileged-identity-management"
  - "request"
image: "/assets/images/pexels-matilda-wormwood-4099096-1.jpg"
---

In this series, we take a look at Azure Active Directory Identity Governance. This premium feature provides you with all the tools that you need to take and keep control over your (external) identities and access to roles, resources, applications, and groups. In short, Identity Governance gives you three ways to do this:

- **[Azure AD Access Reviews](https://janbakker.tech/active-directory-identity-governance-access-reviews/)** (review membership of groups and access to applications)
- **[Azure AD Privileged Identity Management](https://janbakker.tech/active-directory-identity-governance-privileged-identity-management/)** (manage time-based and approval-based role activation to protect your resources with just-in-time and just-enough privileged access) 
- **[Azure AD Entitlement Management](https://janbakker.tech/azure-active-directory-identity-governance-azure-ad-entitlement-management/)** (manage identity and access lifecycle at scale, by automating access request workflows, access assignments, reviews, and expiration) _(This blog post)_

## What is Azure AD Entitlement Management and why should you use it?

The first days of new jobs are exciting. You are pumped with energy, and everything is new; your colleagues, the coffee (or tea), your new notebook and smartphone, the tools, applications, culture, buildings, and so on. Some organizations have a strict onboarding process in place, some don't. If this is missing, the first days on a new job can be a real struggle.

Azure AD Entitlement Management can help you and your organization to be productive from day one. With access packages, you can be assured that your employees can access the right applications, teams, files, resources, and roles. This package can be requested from one single portal, and if configured, the request needs to be approved by the package owner first.

## What's an access package?

An access packages can contain groups or teams, applications and SharePoint sites.

![](/assets/images/image-25.png)

As I explained in my [previous post about Privileged Identity Management](https://janbakker.tech/active-directory-identity-governance-privileged-identity-management/), access to resources and admin roles can now be integrated with Azure AD Groups. The so-called role-assignable groups can also be included in an access package.

## Let's create your first access package!

To give you an idea of what is possible, let's create a new access package. Since administrators have a big role in this series, I'm going to create a package for my Tier 1 Administrators, but you can apply this to your own use-cases, and make one for your Sales team, a new project that you started, or for your temp hires.

To create a new access package, head over to the Identity Governance section in the [Azure AD admin portal](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/elmEntitlement), and click **\+ New access package**. Enter a name and description for your package. We'll leave the catalog to General. [Learn more about catalogs](https://docs.microsoft.com/en-us/azure/active-directory/governance/entitlement-management-catalog-create).

![](/assets/images/image-26.png)

Next, choose the groups, applications, or SharePoint sites that you want to include in the package. As you can see, the _Tier 1 Admin Group_ is a role-assignable group. For each resource, you have to select the role as well.

![](/assets/images/image-27.png)

In the next step, you can configure who can request the package. This can be either internal or external users. When you want to assign this package directly, pick the last option. In this example, I configure the package to be available to **_internal_** users only. If needed, you can scope it down to specific users or groups (recommended).

![](/assets/images/1967-20-12-2020.png)

On the same page, you can also configure the approval settings. In this example, I have configured one static approver, but you can also redirect the approval to the manager of the requestor.

![](/assets/images/1968-20-12-2020.png)

Currently in preview, you can put out some extra questions for the requestor. This can be used in many use-cases, and you can even add multi-language questions.

![](/assets/images/1969-20-12-2020.png)

Last but not least, you can configure whether this package expires and if an access review is needed. Read all about access reviews in [this blog post](https://janbakker.tech/active-directory-identity-governance-access-reviews/)[.](https://janbakker.tech/active-directory-identity-governance-access-reviews/) Configuring expiration will automatically remove access to the resources. Additionally, you can configure whether users can extend the access package.

![](/assets/images/1970-20-12-2020.png)

To finish the wizard select "**Create**". The access package is now available for the selected users.

## Let's try that out

So, how does it look for an end-user? Take **_Lee Gu_** for example. He's a new hire at Contoso and just finished installing his notebook, ready for his first tasks.

In his browser, he goes to [https://myaccess.microsoft.com/](https://myaccess.microsoft.com/ ). Here, Lee can see all the eligible access packages for his account. After adding the justification and answering the question, the request is sent to Allan, the approver.

![](/assets/images/1981-20-12-2020.png)

Over at Allan's account, the request is shown as pending. He can now go on and approve or deny the request.

![](/assets/images/1977-20-12-2020.png)

Both Allan and Lee are getting notified that the request is approved. During the request, the status can be followed in the portal. The package can easily be shared with other co-workers.

![](/assets/images/image-28.png)

## Access Packages for guest users

Before you can add access packages for **_specific_** organizations, you have to add them as [connected organizations](https://docs.microsoft.com/en-us/azure/active-directory/governance/entitlement-management-organization). Here, you specify both internal en external sponsors, which you _can_ use in the approval process. The external sponsor must be added as a guest first. This step is optional for access packages that are available for all external users. In that case, you select **_All users (All connected organizations + any new external users)_** in the wizard.

![](/assets/images/image-32.png)

There are many use-cases for access packages. One of them is guest users. With Entitlement Management, you can create packages for specific guests.

![](/assets/images/image-31.png)

Guests can use the My Access portal link to request access to the package. Depending on the external party, the authentication is done by either Azure AD, federation, or OTP token.

![](/assets/images/image-34.png)

## Admin controls

Users with either a Global administrator, User administrator, Catalog owner, or Access package manager role can create new packages and see active assignments. You can also directly assign specific users to an access package so that users don't have to go through the process of requesting the access package.

![](/assets/images/image-29.png)

An administrator can also add new resources to the existing packages. Keep note that it can take up to 24 hours before changes apply. Read more: [Change resource roles for an access package in Azure AD entitlement management - Azure Active Directory | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/governance/entitlement-management-access-package-resources#when-changes-are-applied)

An access package can have multiple policies. Here you can differentiate between groups and have different approval or review settings for the same set of resources.

![](/assets/images/image-30.png)

## General Settings

There are a few tenant-wide settings that you can configure.

![](/assets/images/image-35.png)

Here you can set the lifecycle for external users. By default, external users are blocked from your tenant and removed after 30 days after the access package expires. This is the same behavior for access reviews.

## Let's wrap things up

The more you work with Azure AD Identity Governance, the more it comes together. It's really fascinating to see how intertwined it all is. With Entitlement Management you can provide the right resources, time-based and with approval in place. You can even use it for your PIM roles. Using Access Reviews you can keep everything tight and clean. To give you some inspiration, you can use Entitlement Management to:

- provide the right access packages for a new project
- improve your B2B collaboration with access to Teams and applications
- let your users request licenses _(using group-based licensing)_
- let your admins request new admin roles _(using role-assignable groups)_
- give a smooth onboarding to your new hires
- give a smooth and secure offboarding for employees that are leaving the company

There is a lot to cover when it comes to Entitlement Management. I suggest you take a look at the documentation and demo video's:

[What is entitlement management? - Azure AD | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/governance/entitlement-management-overview)  
[Tutorial - Create access package - Azure AD entitlement management | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/governance/entitlement-management-access-package-first)  
[Common scenarios in entitlement management - Azure AD | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/governance/entitlement-management-scenarios)  
[Add a connected organization in Azure AD entitlement management - Azure Active Directory | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/governance/entitlement-management-organization)

Stay safe!
