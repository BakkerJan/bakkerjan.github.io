---
title: "Require trusted location for MFA and SSPR registration"
date: 2020-02-22
categories: 
  - "security"
tags: 
  - "azure-ad"
  - "conditional-access"
  - "mfa"
  - "sspr"
  - "untrusted"
image: "/assets/images/msedge_H421Wlpg98.png"
---

This article shows how you can block MFA and SSPR registrations from untrusted locations using Azure AD Conditional Acces.

When you want to enable MultiFactor Authentication and Self Service Password Reset for your users, they need to register their security settings first. Since the [combined portal](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) arrived, users can do this easily in just one place. Using this combined portal is also a requirement in order to make this possible. Although this portal is still in preview, it has great user experience and wizards run smoothly.

And the good part is: we can control this user action with Conditional Acces. This give's you the flexibility to limit this action to only trusted locations, or even trusted devices if you want to. Users then can only register from the locations that you marked as trusted or specific named locations.

First, let's enable the combined portal for your users. Using the Azure portal, go to **Azure Active Directory**, **User Settings** and go to **Manage user feature preview settings.** Next, select a specific user group, or enable this for all your users.

- ![](/assets/images/msedge_KqZAmK0AgT-1-1024x566.png)
    
- ![](/assets/images/msedge_QvNmHiWlxQ-1024x362.png)
    

Next, create a Conditional Acces policy with the following settings:

1. In Name, Enter a name for this policy. For example, **Combined Security Info Registration on Trusted Networks**.
2. Under **Assignments**, click **Users and groups**, and select the users and groups you want this policy to apply to.
3. Under **Cloud apps or actions**, select **User actions**, check **Register security information (preview)**.

4\. Under **Conditions** > **Locations**.

- Configure **Yes**.
- Include **Any location**.
- Exclude **All trusted locations**.
- Click **Done** on the Locations blade.
- Click **Done** on the Conditions blade.

5\. Under **Access controls** > **Grant**.

- Click **Block access**.
- Then click **Select**.

6\. Set **Enable policy** to **On**. Then click **Save**.

<figure>

![](/assets/images/image-8-1024x322.png)

<figcaption>

Instead of Cloud apps, you can select User Actions

</figcaption>

</figure>

## End-user experience

From an end-user perspective, in order to register for MFA and SSPR, you would go to either [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) or [https://aka.ms/mfasetup](https://aka.ms/mfasetup)

When users do this from an untrusted location, they will see the following error.

![](/assets/images/msedge_H421Wlpg98-1024x989.png)

Blocking MFA and SSPR registration from untrusted locations is one of the [common policies](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/concept-conditional-access-policy-common) Microsoft has documented. It gives the flexibility to tune this to your organization's needs.
