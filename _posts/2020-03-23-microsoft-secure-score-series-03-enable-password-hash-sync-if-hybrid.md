---
title: "Microsoft Secure Score Series – 03 – Enable Password Hash Sync if hybrid"
date: 2020-03-23
categories: 
  - "secure-score"
  - "security"
tags: 
  - "adfs"
  - "azure-ad-connect"
  - "azuread"
  - "leaked-credentials"
  - "password-hash"
  - "password-hash-sync"
  - "passwords"
  - "phs"
  - "sso"
coverImage: "azure-ad-authn-image2.png"
---

## Enable Password Hash Sync if hybrid

_Password hash synchronization is one of the sign-in methods used to accomplish a hybrid identity. Azure AD Connect synchronizes a hash, of the hash, of the user's password from an on-premises Active Directory instance to a cloud-based Azure AD instance. Password hash synchronization helps by reducing the number of passwords your users need to maintain to just one. Enabling password hash synchronization also allows for leaked credential reporting._

* * *

![](/assets/images/azure-ad-authn-image2.png)

In this blog post, we are going to take a look at Password Hash Sync. For readability, I will use "PHS" for the rest of the article.

So what's the case here? This improvement action wants you to enable PHS if you are in a hybrid environment. Hybrid means that you have Azure AD Connect in place, so your on-premises Active Directory is synced to Azure Active Directory. Most companies with hybrid setups use federation or passthrough authentication, so the authentication part is handled on-premises. This has often to do with compliance and security requirements. Another sign-in method is PHS. In this case, the hash (of the hash) of the user's password is synchronized with Aure Active Directory, and users authenticate directly to Azure Active Directory.

For the record, this does not mean that the password itself is being synchronized. The password hash is being encrypted in a very secure way and synced to the cloud afterward. I don't want to go to much into detail here, but if you want to understand how this works exactly, take a look at [this page](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization#detailed-description-of-how-password-hash-synchronization-works), or watch [this video](https://www.youtube.com/watch?v=H0tG7fJk3F0&feature=emb_logo).

If we talk about enabling PHS, there are roughly two options:

1. _Enable PHS on top of your current authentication method. (ADFS or Pass-Trough Authentication for example)_
2. _Move away from your current authentication method to PSH._

* * *

Enabling PHS will give you two advantages right away:

- _Azure Active Directory can detect if your user's credentials are leaked. You can set-up remediation policies so your compromised accounts have to reset their passwords. Because you can fully automate this, no service-desk call or whatsoever is involved._
- _You can use PHS as your backup authentication method. Enable PSH, so that you can flip the switch in case you need it in the future._

* * *

Now, I could ramble on for hours to convince you why you should get rid of ADFS, but that's not what this article is about. This article is based on the recommended actions from Microsoft Secure Score and encourages you to enable PHS, not using it as your default authentication method. But before we continue to that, I'll give you something to chew on:

- Ask yourself **why** you are still using ADFS. Kenneth van Surksum wrote [a nice article](https://www.vansurksum.com/2019/08/05/ask-yourself-if-you-still-really-need-adfs/) on that, so feel free to dig into that one.
- How many ADFS related issues did happen over the last 1 or 2 years? Think about expired SSL certificates, load balancer issues, updates that messed up your servers, database outage and DNS failures.
- How quickly can you restore your ADFS environment in case of an emergency?
- Keep in mind that PHS does not require **any** on-premises infrastructure to authenticate your users.

Okay, enough about that. Let's move on to our main goal today, enabling PHS.

#### Enable Password Hash Sync

If you used Express settings at the installation of Azure AD Connect, PHS is already enabled. If you used custom settings, you can enable this manually. To enable PHS, go to your Azure AD Connect server and start the wizard.

![](/assets/images/image-13.png)

Select the **Customize synchronization options** and click next.

![](/assets/images/mstsc_x30t1JDO9W.png)

Next, log-in using your admin credentials and go to the **Optional Features** section. Make sure that Password hash synchronization is enabled and finish the wizard.

![](/assets/images/mstsc_B0nPdV0RI9.png)

Going to your Azure portal, you should now see that PHS is enabled.

![](/assets/images/image-14.png)

Now you have enabled PHS, you can leave it like this. You continue to use your preferred authentication method. PSH is enabled in case of an emergency and your leaked credentials can be detected from now on.

#### Flip the switch

If you are ready to set PHS as your preferred authentication method, just fire up the Azure AD Connect wizard again and select Change user sign-in. Next, make sure you select Password Hash Sync as your preferred method.

- ![](/assets/images/mstsc_ctWIaiKeZC-1024x776.png)
    
- ![](/assets/images/1632227970-1024x722.png)
    

#### But wait, there is more!

I can imagine that changing your authentication method for your whole domain at once can be a big step. If that's the case, you should check out this preview feature where you can do a staged roll-out of PHS. Just select a pilot group and work from there. [Read more](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-staged-rollout).

![](/assets/images/image-15.png)

#### A few things to take note of

Enabling PHS is relatively easy to configure. You should always consider the consequences before taking this step.

- Both your password complexity policy and password expiration policy are affected. [Read more.](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization#password-policy-considerations)
- Password hashes are synced every **2** minutes.
- The synchronization of a new password has no impact on the Azure user who is signed in. This session keeps working, depending on your time-out settings.
- If your organization uses the accountExpires attribute as part of user account management, this attribute is not synchronized to Azure AD. The account will stay active in Azure AD.

Stay safe!
