---
title: "Use Registration campaign to promote Microsoft Authenticator App"
date: 2021-11-28
categories: 
  - "entra"
  - "security"
image: "/assets/images/2edit-pexels-andrea-piacquadio-3758104.jpg"
---

With all the [new improvements](https://janbakker.tech/enable-location-information-and-code-match-for-azure-mfa/) to the Microsoft Authenticator App, this seems a good time to highlight a new capability in Azure AD: **Registration Campain**, also known as the [nudge feature.](https://aka.ms/nudgedoc) Also, organizations [should move away from phone transports for authentication.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752) If your users use text (SMS) for second-factor authentication, they have very little context, and therefore might be confusing. On top of that, attackers use SIM jacking techniques to bypass those phone methods.

<figure>

![](/assets/images/file.jpeg)

<figcaption>

Not that much context with SMS/Text

</figcaption>

</figure>

By far the most secure way to authenticate is passwordless authentication, so to step up from phone methods, Authenticator App would be a good step to strengthen your security posture.

![Tabel met de sterke punten en voorkeursverificatiemethoden in Azure AD](/assets/images/authentication-methods.png)

## Registration campain

Today we take a look at a new feature that will prompt your users to move towards Authenticator App. There are a few requirements to be aware of:

- Your organization must have enabled Azure MFA.
- User must not have already set up Microsoft Authenticator for push notifications on their account.
- Admins need to enable users for Microsoft Authenticator using one of these policies:
    - MFA Registration Policy: Users will need to be enabled for Notification through mobile app.
    - Authentication Methods Policy: Users will need to be enabled for the Microsoft Authenticator and the Authentication mode set to **Any or Push**. If the policy is set to Passwordless, the user will not be eligible for the nudge.

Enabling the Authenticator app is easy, and can be done in a few clicks. In the Azure portal, go to **_Azure Active Directory -> Security -> Authentication methods._** Enable for all users, or pilot for selected users first.

![](/assets/images/image-8.png)

Within the policy, you should have either configured 'Any' or 'Push'.

![](/assets/images/image-9.png)

So, with all the requirements in place, let's see where we can configure the registration campaign. In the Azure portal, head over to **_Azure Active Directory -> Security -> Authentication methods_**. Here we will find the Registration Campaign blade.

![](/assets/images/image-10.png)

Configuring this feature is pretty straightforward.

1. To enable the feature, select **Enabled**.
2. Select the days that the user can snooze. **0** (zero)days means that the user is prompted every day. The user can still skip the wizard, but is reminded on daily base.
3. By default, the policy is enabled for all users, but you can exclude users or groups as well.
4. Select either all users, or select a few test users first.

![](/assets/images/image-12.png)

In this example, I have enabled it for my test user: Chandler Bing. Chandler has registered for Azure MFA using his mobile phone number.

![](/assets/images/image-13.png)

## End-user experience

Now, what does it look like for the end-user? Chandler signs in, like always, and is prompted to do Azure MFA. After this was successful, he is now prompted for the second time to enroll the Authenticator app.

![](/assets/images/image-14.png)

Chandler can snooze this message by clicking "Not now", or click Next to start the registration. Now this will just be the standard enrollments procedure that is used for MFA and SSPR registration. After finishing the wizard, the Authenticator App is now set as the default sign-in method.

![](/assets/images/image-15.png)

Good to mention: the phone method is not deleted, and can still be used as a backup method.

![](/assets/images/image-17.png)

## Track the results

Events can be easily tracked by the reporting module in the Azure portal, or via the audit logs.

- ![](/assets/images/1638108612.png)
    
- ![](/assets/images/image-16.png)
    
- ![](/assets/images/1638108767.png)
    

## Wrap things up

As you can see, this new feature works pretty smoothly. A couple of things you should know:

- You can run the campaign for as long as you want. Just disable the feature to stop the campaign.
- If selected, guest users will also be prompted.
- The feature works with the Authenticator app only.
- A user who has set up Microsoft Authenticator app only for TOTP codes also will see the nudge.
- This feature can also be configured using the [Graph API](https://docs.microsoft.com/en-us/azure/active-directory/authentication/how-to-mfa-registration-campaign#enable-the-registration-campaign-policy-using-graph-explorer).

Learn more: [Nudge users to set up Microsoft Authenticator app - Azure Active Directory | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/authentication/how-to-mfa-registration-campaign#enable-the-registration-campaign-policy-using-graph-explorer)

Stay safe!
