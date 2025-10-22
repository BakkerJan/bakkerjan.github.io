---
title: "Get started with Azure AD B2B direct connect"
date: 2022-05-28
categories: 
  - "entra"
coverImage: "pexels-pixabay-272254.jpg"
---

We all love seamless collaboration, right? Well, here's a new feature that might improve that experience. Today, we will talk about Azure AD's cross-tenant access settings, and in particular, about Azure AD B2B direct connect.

## What is B2B direct connect?

B2B direct connect is part of the cross-tenant access settings in Azure AD. These settings will give you granular control over how external Azure AD organizations collaborate with you (inbound access) and how your users collaborate with external Azure AD organizations (outbound access).

With B2B direct connect, administrators can set up mutual trusts between external Azure AD tenants. At the moment of writing, this feature only supports Microsoft Teams shared channels.

Next to that, you can configure to trust claims from external Azure AD tenants like MFA or device claims. This is Microsoft's answer to the "_double prompt issue_" for external Azure AD users. More on that later.

## Licensing

When you configure trust settings or apply access settings to specific users, groups, or applications, you'll need an Azure AD Premium P1 license. It is not very clear how that relates to external identities, but I think we can assume that the monthly active users [(MAU) billing concept](https://docs.microsoft.com/en-us/azure/active-directory/external-identities/external-identities-pricing) applies to this feature as well. That means, that your first 50,000 MAUs per month are free for both Premium P1 and Premium P2 features.

## Let's get started

Okay, let's build this, shall we? In order to set up a mutual trust, we need to do stuff on both the resource tenant and the external tenant. For that purpose, I have set up two tenants:

Company A (resource tenant)  
Company B (external tenant)

To show the capabilities of B2B direct connect, I've also created a group in the external tenant. Only users in that group can make use of the mutual trust, or in this case, added to the Teams shared channel.

![](/assets/images/Whiteboard-1.png)

Let's start with Company A, the resource tenant. In order to set up a trust with Company B, we need to know two things:

- The tenant ID from Company B
- The group ID of the collaboration group (optional)

There a several ways to identify the tenant ID. In our case, the administrator from Company B should provide us with this information that can be easily found on the Azure AD overview page in the Azure portal.

![](/assets/images/image-22.png)

So, on the Company A side, head over to the Azure portal and navigate to **_Azure Active Directory -> External Identities_**. Next, go to Cross-tenant access settings (Preview) and **\+ Add organization**. Then, enter the tenant ID from Company B, and the name should be resolved. To add Company B, click the **Add** button.

![](/assets/images/image-25.png)

When Company B is added successfully, click on _**Inherited from default**_ to edit the inbound access settings for this specific trust.

![](/assets/images/image-26.png)

Next, go to the B2B direct connect tab, and select **_Customize settings_** to overrule the default settings. In the access status section, choose **Allow access**. Then, select the _**Select external user and groups**_ radio button, and enter the group ID from Company B. Also change the type from user to **group**, and click **Add**.

![](/assets/images/image-34.png)

Next, head over to the **_Applications_** tab and select Allow access. Note that both users and groups and application types must match in order to save the configuration.

![](/assets/images/image-33.png)

Now, over at Company B, we do the exact same thing, but now we enter the tenant ID from Company A instead.

![](/assets/images/image-28.png)

After Company A is added, we configure the **outbound** settings for this trust.

![](/assets/images/image-29.png)

From the settings pane, go to the B2B direct connect page, and select Customize settings,

![](/assets/images/image-31.png)

Next, head over to the External applications tab, and select Allow access. Note that both users and groups and application types must match in order to save the configuration.

![](/assets/images/image-32.png)

Okay, so what have we done so far? We've set up a trust between company A and company B, but limited the collaboration to a specific group. That group lives in the Company B tenant. Only users in that group can access resources in Company A.

By default, B2B direct connect is blocked for all users, but by editing the settings for a specific trust, we overruled the defaults. Note that this only applies to this specific trust and that there are actions needed from both sides to set this up.

Keep in mind that these settings may take some time before there are applied. In my lab, I could successfully add external users to Teams shared channels after waiting 30-90 minutes. That also counts for adding users to the group later. Those changes are not instantly working.

## End-user experience (Teams shared channels)

At the time of writing, Azure B2B direct connect only supports [Teams shared channels](https://docs.microsoft.com/en-us/microsoftteams/shared-channels). Shared channels is in preview and require that you have configured Microsoft Teams Public Preview. If you plan to share channels with other organizations, they must also have configured [Teams public preview](https://docs.microsoft.com/en-us/MicrosoftTeams/public-preview-doc-updates).

![](/assets/images/image-35.png)

After this feature is enabled, team owners can create a new shared channel.

Now, let's create a shared channel in Company A and add Adele from Company B to that channel. Let's see that in action. Note how quick and seamless this experience works. I'm impressed!

## Wrap it up

B2B direct connect is currently in preview, but from what I've seen this will bring a whole lot of good stuff to Microsoft 365. Now, a couple of remarks and first thoughts.

Do know that B2B is disabled by default. To create mutual trust, actions from both sides are needed. And that's a good thing! Also, if we dive into the trust settings, we can also trust MFA and device claims from other Azure AD tenants. Now as this seems a good thing at first, there are multiple security considerations. Imagine how this can be used for lateral movement from one tenant to another. I will write more about this topic in a later blogpost.

One last thing. You can create access reviews for B2B direct connect users via shared channels in Microsoft Teams. As you collaborate externally, you can use Azure AD access reviews to make sure external access to shared channels stays current. Learn more: [Create an access review of groups and applications - Azure AD | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/governance/create-access-review?WT.mc_id=Portal-Microsoft_AAD_ERM#include-b2b-direct-connect-users-and-teams-accessing-teams-shared-channels-in-access-reviews-preview)

![](/assets/images/image-36.png)

That's it for today. Stay safe!
