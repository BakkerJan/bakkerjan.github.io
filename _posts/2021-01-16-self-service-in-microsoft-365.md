---
title: "Self Service in Microsoft 365"
date: 2021-01-16
categories: 
  - "entra"
tags: 
  - "delegation"
  - "groups"
  - "it-staff"
  - "microsoft365"
  - "my-apps"
  - "my-groups"
  - "my-sign-ins"
  - "my-staff"
  - "password-reset"
  - "self-service"
coverImage: "pexels-karolina-grabowska-5650028.jpg"
---

One of the great things about Azure Active Directory is the capability of self-service. Maintaining security groups can be a laborious and cumbersome task to do. The same applies to resetting passwords, handing out licenses, permissions, applications, and privileged roles. What if I told you that you can delegate most of these tasks to your end-users, their managers, or product-and application owners? In this blog post, I will show you the built-in capabilities of self-service in Azure Active Directory, which is underlying to Microsoft 365. Some of them you might already know, some might be new to you.

In this article we take a look at the different portals that offer self service in Microsoft 365:

1. [My Apps](https://myapplications.microsoft.com/)
2. [My Staff](https://mystaff.microsoft.com/)
3. [My Access](https://myaccess.microsoft.com/)
4. [My Account](https://myaccount.microsoft.com/)
5. [My Groups](https://account.activedirectory.windowsazure.com/r#/groups)
6. [My Sign-ins](https://mysignins.microsoft.com/)
7. [Self Service Password Reset](https://passwordreset.microsoftonline.com/)

## 1\. [My Apps](https://myapplications.microsoft.com/)

In my opinion, this feature is way undervalued. [My Apps](https://myapplications.microsoft.com/) can be your users' landing page for day-to-day work. By default, it will display all your Azure AD and Office 365 apps, which you can also categorize in different collections. Users are able to create their own collections as well.

![](/assets/images/image-13.png)

App collections can be targeted to specific users and groups, and each collection can have multiple owners. This means they'll have full editing rights to this collection. At the time of writing, the owner can only manage these collections from the Azure Active Directory portal. Collections that are created by the user in the MyApps portal, can be edited from the MyApps portal itself, by clicking the Manage button at the top.

![](/assets/images/image-16.png)

Strangely enough, the buttons to create and manage collections are not shown by default. You have to use [https://myapplications.microsoft.com/?endUserCollections](https://myapplications.microsoft.com/?endUserCollections) in order to see the buttons.

## My Apps Self Service

So what about the self service Jan? Well, under the ellipsis, the user can request new applications.

![](/assets/images/image-17.png)

Users can request new applications that are enabled for self-service. This can be done with or without approval. In case of approval is required, the application owner will get the request. In this example, Christie is requesting an application that Adele owns.

![](/assets/images/image-18.png)

![](/assets/images/2203-12-01-2021.png)

![](/assets/images/2204-12-01-2021.png)

## 2\. [My Staff](https://mystaff.microsoft.com/)

[My Staff](https://mystaff.microsoft.com/) is built on top of Administrative Units. To know more about that, please reach out to my [previous blog post.](https://janbakker.tech/a-first-look-at-administrative-units-and-my-staff-in-azure-active-directory/) In short, with My Staff, a user who can't access their account can regain access in just a couple of clicks, with no helpdesk or IT staff required.

My Staff can be enabled in the Azure admin portal under **_Azure Active Directory -> User Settings -> Manage user feature preview settings_**. Only users who've been assigned an admin role can access My Staff. If you enable My Staff for a user who is not assigned an admin role, they won't be able to access My Staff.

<figure>

![](/assets/images/image-19.png)

<figcaption>

My Staff settings in the Azure portal

</figcaption>

</figure>

With My Staff, administrators can:

- Add and update phone numbers
- Reset passwords
- Enable phone sign-in

So this adds a couple more use cases to the use of Administrative Units. To give you some ideas:

- Teachers can reset passwords for their students.
- Team managers can add and edit phone numbers of their team to use for multi-factor authentication.

The My Staff portal is mobile friendly. Take a look at this short video and see how easy and smooth a user password reset is done. No manual needed for this, this works really intuitive!

![](/assets/images/o9xWb6eksm.gif)

## 3\. [My Access](https://myaccess.microsoft.com/)

[My Access](https://myaccess.microsoft.com) is your go-to-portal for requesting access packages and performing access reviews. Check out my previous articles about [Entitlement Management](https://janbakker.tech/azure-active-directory-identity-governance-azure-ad-entitlement-management/) and [Access reviews](https://janbakker.tech/active-directory-identity-governance-access-reviews/) for a deep-dive on these subjects.

In short, this is the place where your users can request access to groups, teams, applications, and SharePoint sites. In fact, this means that you can use self-service for pretty much anything, as long as it is backed with Azure AD groups. My Access can be used to:

- provide the right access packages for a new project
- improve your B2B collaboration with access to Teams and applications
- let your users request licenses _(using group-based licensing)_
- let your admins request new admin roles _(using role-assignable groups)_
- give a smooth onboarding to your new hires
- give a smooth and secure offboarding for employees that are leaving the company

![](/assets/images/image-12.png)

## 4\. [My Account](https://myaccount.microsoft.com/)

My Account should be known by all your users. This portal provides various self-service tools related to identity, security, devices, and Office Apps. I will not go over every little detail, since the portal works very intuitive, but with the My Account portal, your users can:

- Update their security information for MFA, Self Service Password Reset, and passwordless authentication methods;
- Change their password;
- Leave guest organizations;
- Manage their enrolled and registered devices;
- See a list of recent sign-ins and be able to take action if suspicious sign-ins occurred;
- Install their (mobile) Office apps and Skype for Business;
- See all their licenses and subscriptions;
- Revoke app permissions;
- Get access to various tools for troubleshooting, and language packs for Office apps.

![](/assets/images/image-20.png)

## 5\. [My Groups](https://account.activedirectory.windowsazure.com/r#/groups)

The My Groups portal works similar to the My Apps portal, but instead of applications, users can manage groups and group memberships. Users see a list of groups that they own, and the ones that they can join. By default, users can create new groups, both security, and Microsoft 365 groups.

![](/assets/images/image-21.png)

To manage the behavior of this portal, you can configure the Azure AD Groups settings.

<figure>

![](/assets/images/image-22.png)

<figcaption>

Enable or disable the ability to create groups

</figcaption>

</figure>

<figure>

![](/assets/images/image-23.png)

<figcaption>

You can control which types of groups can be created by your users

</figcaption>

</figure>

<figure>

![](/assets/images/image-24.png)

<figcaption>

Self Service Group Management can be enabled or disabled

</figcaption>

</figure>

It's also important that you take care of the naming policy and blocked words. This will prevent that users can create groups like HR or Sales and that the groups can easily be filtered based on prefix or suffix.

![](/assets/images/image-32.png)

## 6\. [Self Service Password Reset](https://passwordreset.microsoftonline.com/)

I started my career on the service desk to help out users with IT-related problems. One of the most asked questions was: "can you reset my password?". If done correctly, this is a cumbersome task, since you have to take care of ticket registration, verification of the caller, and the password reset itself. Azure AD provides a self-service password tool, where users can reset their own passwords, without intervention from IT staff and in a secure way.

![](/assets/images/image-26.png)

If you are an administrator, I recommend you to read [this](https://janbakker.tech/microsoft-secure-score-series-05-enable-self-service-password-reset/), and [this](https://janbakker.tech/what-admins-should-know-about-the-combined-registration-portal-for-azure-mfa-and-self-service-password-reset/) blog post about setting up Self Service Password Reset. Back in 2020, I talked to Richard Campbell about this subject on a podcast. You can find it here:

[Self-Service Passwords with Jan Bakker - RunAsRadio](http://runasradio.com/Shows/Show/748)

![](/assets/images/image-25.png)

## 7\. [My Sign-Ins](https://mysignins.microsoft.com/)

The last portal is all about Sign-ins and is a sub portal of the My Account portal. I'd like to point out this one too because it gives your users insights into the sign-ins that are happening on their account. When the sign-ins look unfamiliar, the user can report the sign-in as unsafe, and more importantly, check out and change their security information.

![](/assets/images/image-30.png)

<figure>

![](/assets/images/image-31.png)

<figcaption>

Users can take control of their current sessions

</figcaption>

</figure>

## Wrap things up

Seeing this list of self-service portals, it cannot be unseen how all of this is working together. Access can be requested based on approvals and can be reviewed periodically. Managers can help out their team so that IT can focus on the bigger picture. Users are in control of their sign-ins and can reset passwords from everywhere, on every device, at any time. Licenses, applications, and roles can be requested and approved in the blink of an eye.

Of course, there should always be a consideration of how this will fit into your organization. In some cases, it would not be desirable to let users create their own groups for example. I would recommend making use of self-service as much as possible to provide more productivity in your organization.

Stay safe!
