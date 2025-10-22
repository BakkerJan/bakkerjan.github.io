---
title: "Change billing model for Azure AD guest users"
date: 2021-06-16
categories: 
  - "entra"
image: "/assests/images/pexels-suzy-hazelwood-4056856-scaled.jpg"
---

Back in 2020, Microsoft [announced](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-active-directory-external-identities-goes-premium-with/ba-p/1604572) a change in the pricing model for External Identities. The change is mentioned in this blog post, but it did not get that much attention if you ask me. So, to make you all aware, let's see whats this change is all about, and how your organization can benefit from this.

## What's the case here?

The "old" pricing model is based on a 1:5 ratio, meaning that you could serve 5 guest users for every premium license in your tenant. This is automatically calculated. For example, if you have **100** guest users that make use of Conditional Access, you need to have at least **20** Azure AD P1 licenses. Same goes for Azure AD P2.

In the new pricing model, the billing is changed to the "MAU model". MAU stands for Monthly Active Users. This model is more predictable and flexible, and the most important: free for the first 50.000 guest users. That's right, **free**. So you might only need to buy 1 single P2 license for your admin to upgrade your tenant to P2, and configure the settings, but after that, you can provide 50.000 guest users with all the premium goodness out there for free.

To clarify even more, the new pricing model look's like this:

![](/assets/images/image.png)

Keep note that this model can change slightly over time, so make sure you check the current prices [here](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/).

## How to migrate to the new billing model?

Changing to the new model is pretty straightforward. The only thing you'll need is an Azure subscription. This can be either a new or existing subscription. In order to enable the new pricing model, you'll need to link your Azure AD tenant to a subscription.

Go to the [Azure portal](https://portal.azure.com), and look for **Azure AD** - **External Identities**. Next, click Linked Subscriptions in the left pane. As you can see, this tenant is not linked yet, and therefore still using the old pricing model.

![](/assets/images/image-1.png)

Next, select the tenant, and click **_Link subscription_**. Then, select your subscription and resource group. Next, click **_Apply_** to finish.

![](/assets/images/image-2.png)

By switching to pricing based on Monthly Active Users (MAU), you will no longer be limited to a 1:5 Azure AD to guest license ratio. Save with your first 50,000 MAU free and pay only for what you use. You will not be billed until your usage exceeds 50.000 MAU.

![](/assets/images/image-5.png)

You can be prompted with the following error: _The subscription is not registered to use namespace 'Microsoft.AzureActiveDirectory'. See https://aka.ms/rps-not-found for how to register subscriptions._

![](/assets/images/image-3.png)

To fix that, register the Microsoft Azure Active Directory service under the Resource providers section within the subscription.

![](/assets/images/image-4.png)

## Wrap things up & considerations

This new pricing model can be a very welcome feature for many organizations, meaning they no longer have to worry about buying licenses for their guest and external users. Good to know: this model applies to both B2B and B2C users.

But there are some things to consider here. According to [this article](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/), there is an additional flat fee of $0.03 / €0.026 for each SMS/Phone-based multi-factor authentication attempt, failed or successful. This is also mentioned in the FAQ:

![](/assets/images/image-11.png)

Apparently this is also billed for the first 50.000 MAU, so there might be a chance that you pay more than you to in the current billing model. If you use Authenticator App for MFA, there is no additional fee.

To learn more, please check out the following articles:

[Pricing - Active Directory External Identities | Microsoft Azure](https://azure.microsoft.com/en-us/pricing/details/active-directory/external-identities/)  
[MAU billing model for Azure AD External Identities | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/external-identities/external-identities-pricing)  
[Azure Active Directory External Identities goes premium with advanced security for B2C - Microsoft Tech Community](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-active-directory-external-identities-goes-premium-with/ba-p/1604572)

Stay safe!
