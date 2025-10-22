---
title: "10 productivity tips for M365 administrators"
date: 2021-10-23
categories: 
  - "entra"
coverImage: "pexels-miguel-a-padrinan-1061133.jpg"
---

I have worked with Microsoft 365 over the past few years, and every now and then you learn a new trick. When that moment comes, your work is a little more pleasant, easier, or more productive from that moment forward. I'd like to share some of that tips in no particular order.

## Tip 1 - Hello darkness my old friend

Dark themes is a thing in the IT world. You either hate or love it. Did you know the Azure portal also supports a dark theme? Go to the **settings** wheel, and pick **Appearance + startup views**.

![](/assets/images/1634061295.png)

## Tip 2 - Save a few clicks in Azure AD

Here's a tip that might save you some time in the future! When creating a new user, group, or application, you can easily do that from the Overview page in Azure AD.

![](/assets/images/1634060524.png)

## Tip 3 - MFA settings portal

When you need to edit your MFA service settings, it takes quite some clicks to finally get there. Usually, you would go from the **Azure portal -> Azure Active Directory -> Security -> Additional cloud-based MFA settings**.

However, you can also use this short URL: [https://aka.ms/mfaportal](https://aka.ms/mfaportal)

![](/assets/images/1634060829.png)

## Tip 4 - Microsoft Cloud App Security portal

Just like the previous tip, the same goes for the Microsoft Cloud App Security portal. Simply use [https://aka.ms/mcasportal](https://aka.ms/mcasportal) to go straight to your administration panel for MCAS.

![](/assets/images/1634060883.png)

## Tip 5 - Time out settings

I landed on this tip before in a [previous blog post](https://janbakker.tech/secure-your-azure-management-portal/), but I'd like to point it out here as well. You can easily configure the time-out setting in the Azure portal if you like.

![](/assets/images/1634060281.png)

## Tip 6 - Free stuff

As you might know, I'm Dutch. And the Dutch like free stuff. Did you know you can bump up your Azure AD tenant to Azure AD P2 (P1 included) for 30 days and 100 users? Time to get started with Conditional Access, Dynamic Groups, and Entitlement Management!

![](/assets/images/1634060624.png)

## Tip 7 - Identity Protection / risky users

This one is related to the free P2 license in the previous tip. With that enabled, you get instant access to Identity Protection and your risky users dashboards. Especially when you have [password hash sync enabled](https://janbakker.tech/microsoft-secure-score-series-03-enable-password-hash-sync-if-hybrid/), you'd be surprised what insights you get from the risky users or risk detections section. Bonus tip: use this data to get some extra security budget from the C-level folks. It's even more "fun" when the accounts of high privileged users or priority accounts pop up in that dashboard.

![](/assets/images/image-26.png)

## Tip 8 - User features

Azure Active Directory has several user features that are disabled by default. Especially for older tenants, these features might be still disabled. Take a look at those settings to see if it has value for you or your end-users. The second feature (combined registration portal) is highly recommended if you use Azure MFA and/or Self Service Password reset. The settings can be found in the **Azure portal -> Azure Active Directory -> User Settings -> Manage user feature settings** (pfew, anyone knows the aka.ms shortcut for that one? )

![](/assets/images/image-25.png)

## Tip 9 - Group naming policies

When you allow self-service when it comes to groups and teams (you should), governance is important. Azure AD has some settings that you can use to end up with cleaner names. Bonus tip: you can also configure expiration policies for automatic clean-up of abandoned groups or teams.

![](/assets/images/image-27.png)

## Tip 10 - Usage & Insights

Azure AD has several dashboards that might be useful in your day-to-day work as an administrator. This is also very valuable data for management presentations. Check out the insights on authentication methods and usage on password resets for example.

![](/assets/images/image-28.png)

## Tip 11 - <YOUR TIP HERE?>

Do you have a brilliant tip that you like to share with the world? Ping me on Twitter, LinkedIn, or email!

Stay safe!
