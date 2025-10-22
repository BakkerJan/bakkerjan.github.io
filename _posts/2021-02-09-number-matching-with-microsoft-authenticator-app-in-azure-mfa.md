---
title: "Number matching with Microsoft Authenticator App in Azure MFA"
date: 2021-02-09
categories: 
  - "entra"
  - "security"
image: "/assests/images/image-6-1.png"
---

Number matching and passwordless phone sign-in. I was used to it for a couple of months already because this feature was previously launched for personal Microsoft accounts like Outlook or Hotmail. It's now available (preview) in Azure AD to use with your work or school account.

![](/assets/images/image-6.png)

When this feature is enabled, users are asked to match the number in the sign-in screen with the number in the Authenticator app. After that, the user needs to authenticate through PIN or biometric like fingerprint or face-ID to complete the authentication flow.

## Prerequisites

Your tenant must be enabled for MFA with push notifications through the Microsoft Authenticator app in order to use this method. Also, make sure the [combined registration portal](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) is enabled.

To enable the passwordless feature with number matching, access the [MFA additional settings portal](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx?tenantId=39a5568b-aafd-430a-a6a5-715bd29ce240&culture=en-us&requestInitiatedContext=users) (the very ugly one) and check if the Authenticator app push notification is checked.

![](/assets/images/image-2.png)

## Phone sign-in

To use this passwordless feature, your phone needs to be _Azure AD registered_ (not MDM). If the user is eligible for passwordless sign-in, the feature can be enabled in the Authenticator app.

![](/assets/images/IMG_0023.png)

Make sure that the user has configured the default sign-in method to Microsoft Authenticator - notification. This can be done via **https://aka.ms/mfasetup**

![](/assets/images/image-4.png)

## Enable passwordless sign-in

To enable the authentication method for passwordless phone sign-in, complete the following steps:

1. Sign in to the [Azure portal](https://portal.azure.com/) with a _global administrator_ account.
2. Search for and select _Azure Active Directory_, then browse to **Security** > **Authentication methods** > **Policies**.
3. Under **Microsoft Authenticator (preview)**, choose the following options:
    1. **Enable** - Yes or No
    2. **Target** - All users or Select users
4. Each added group or user is enabled by default to use Microsoft Authenticator in both passwordless and push notification modes ("Any" mode). To change this, for each row:
    1. Browse to **...** > **Configure**.
    2. For **Authentication mode** - Any, Passwordless, or Push
5. To apply the new policy, select **Save**.

![](/assets/images/image-3.png)

## Experience - first time

The first time a user starts the phone sign-in process, the user performs the following steps:

1\. Enters the username on the sign-in page.  
2\. Selects Next.  
3\. If necessary, select _"Other ways to sign in_", or _"use an app instead_"  
4\. Selects Approve a request on my Microsoft Authenticator app.  
5\. The user is then presented with a number. The app prompts the user to authenticate by selecting the appropriate number, instead of by entering a password.

## Experience after the first time

When the user has signed-in using the new passwordless method, the next time this method is used automatically. The user can then switch to a password if needed. Check out this short video to see what the experience is like:

## Read more

Get started with this new feature today! You can enable this for just a couple of users, or for your entire organization. Your users will love it! The full documentation can be found here:

[Passwordless sign-in with the Microsoft Authenticator app - Azure Active Directory | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-authentication-passwordless-phone)

Stay safe!
