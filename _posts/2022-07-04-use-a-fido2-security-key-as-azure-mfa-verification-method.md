---
title: "Use a FIDO2 security key as Azure MFA verification method"
date: 2022-07-04
categories: 
  - "entra"
  - "security"
image: "/assests/images/IMG_3920-scaled.jpg"
---

This news seems to be kept under the radar a little bit, but I wanted to point out a new feature in Azure AD that might help out some organizations with their Azure MFA implementations. Take a look at this list of supported authentication methods, and notice that [passwordless methods](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-authentication-passwordless) can also be used as a form of verification for Azure AD Multi-Factor Authentication:

- Microsoft Authenticator app
- Windows Hello for Business
- **FIDO2 security key**
- OATH hardware token (preview)
- OATH software token
- SMS
- Voice call

Now, I was surprised to see FIDO2 security key popping up in this list. Let's talk about that one.

## The everlasting question

During every Azure MFA implementation I got the same question over and over again: What if the user doesn't want to use their (personal) mobile phone for verification? Up until now, OATH hardware token was the only option here. Although this works pretty well (check out [this blog post](https://allthingscloud.blog/oath-totp-hardware-tokens-with-azure-multi-factor-authentication/) by Oktay), we have a new kid on the block now: FIDO2 security keys.

FIDO2 security keys can be used for a passwordless experience in Azure AD, where it replaces the password entirely. But it can also be used as a verification method for Azure MFA now. That brings another option to the table when we talk about this specific use case.

Using FIDO2 keys instead of OATH hardware keys can have some benefits:

- **Delegation**. Enabling OATH tokens for Azure MFA is labor-intensive. FIDO2 keys can be set up by the end-users, so will take away the burden from IT.
- **Support**. FIDO2 is supported by many more platforms, so your users can use the same key to protect both work and school accounts as their social accounts like [Twitter](https://www.inthecloud247.com/secure-your-twitter-account-with-a-fido-security-key/) and [Google](https://support.google.com/accounts/answer/6103523?hl=en&co=GENIE.Platform%3DAndroid).
- **Future**. As stated before, FIDO2 keys support passwordless sign-in. So, when that next passwordless project comes along, your users are already equipped with the right hardware.
- **Costs.** It depends on what type of key you buy, but some vendors ship keys for less than $10.

##### Update 07/12/2022 (is it phishing resistant?)

I've tried to phish tokens with the use of [Evilginx](https://github.com/kgretzky/evilginx2), and the one where I used a FIDO2 key as an MFA method could not be captured. Capturing the token (cookie) would work with any other MFA method, like the Authenticator app and SMS.

![](/assets/images/image-11-1024x134.png)

## How to set up?

Before we can start, let's make sure that the authentication methods are available for all users, or the specific user group(s) that need them. For this use case we need 2 methods:

1. Temporary Access Pass (onboarding & recovery)
2. FIDO2 Security Key

These settings can be found in the Azure portal under **_Azure Active Directory -> Security -> Authentication methods._**

![](/assets/images/image-1.png)

Why do we need a Temporary Access Pass for onboarding, you may ask? This is needed to satisfy the MFA requirement for FIDO2:

![](/assets/images/image-9.png)

When using a Temporary Access Pass, users don't need to set up an MFA method first.

**Important!** If not already enabled, make sure the combined registration portal is enabled, to support FIDO2 security keys registration: [Combined registration for SSPR and Azure AD Multi-Factor Authentication - Azure Active Directory - Microsoft Entra | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

Next, let's create a new Temporary Access Pass (TAP) for the user. If you are new to this, please read this article first: [Onboard FIDO2 keys using Temporary Access Pass in Azure AD - JanBakker.tech](https://janbakker.tech/onboard-fido2-keys-using-temporary-access-pass-in-azure-ad/)

![](/assets/images/image-5.png)

Next, IT staff needs to provide the details to the end-user.

![](/assets/images/image-6.png)

If not done already, make sure that MFA is enforced for your users. I created this Azure AD Conditional Access policy to enforce MFA on all cloud apps.

![](/assets/images/image-8.png)

Now that all the configuration in the back-end is done, let's take a look at the end-user experience.

## End-user experience

First, the user needs to register the FIDO2 key, so it will be connected to their Azure AD / Office 365 account. To satisfy the MFA requirement, we use the Temporary Access Pass to sign in to the registration portal: [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)

![](/assets/images/image-2.png)

Because the user is signed in with strong credentials, they can go straight to the security portal, and add a new method.

![](/assets/images/image-3.png)

Next, the user will follow the steps to register the key, and gives it a proper name.

![](/assets/images/image-4.png)

After this is done, the user is ready to go. Next time the user wants to sign in to Office 365 for example, the user provides the username and password. Next, the user is prompted for MFA, which they can verify using their FIDO2 security key.

![](/assets/images/msedge_GmIbdb5Fxi.gif)

You can keep track of the Azure AD sign-in logs to verify which methods are used.

![](/assets/images/image-7.png)

## Let's wrap things up

Now, as this might help out a few organizations on their MFA journey, I really hope they take it a step forward and replace Azure MFA for a true passwordless sign-in, using either Windows Hello for Business, FIDO2 security keys, or the Microsoft Authenticator app. For the time being, this method can also be used to [step away from phone-based MFA methods](https://techcommunity.microsoft.com/t5/microsoft-entra-azure-ad-blog/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752) like SMS, as they’re the least secure of the MFA methods available today.

Learn more:

[Onboard FIDO2 keys using Temporary Access Pass in Azure AD - JanBakker.tech](https://janbakker.tech/onboard-fido2-keys-using-temporary-access-pass-in-azure-ad/)  
[Combined registration for SSPR and Azure AD Multi-Factor Authentication - Azure Active Directory - Microsoft Entra | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)
