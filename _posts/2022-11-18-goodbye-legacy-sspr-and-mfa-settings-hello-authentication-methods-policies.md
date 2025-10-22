---
title: "Goodbye legacy SSPR and MFA settings. Hello Authentication Methods Policies!"
date: 2022-11-18
categories: 
  - "entra"
  - "security"
coverImage: "pexels-antoni-shkraba-5816307-scaled.jpg"
---

I've got some exciting news to share today. Microsoft has launched a public preview called **_"Authentication Methods Policy Convergence."_** I was part of the private preview program, and I'm very happy to see this feature going public. In this post, I will give you a brief introduction to this new feature and explain why this is such a big deal.

## Current situation

A while back, I wrote [this post](https://janbakker.tech/what-admins-should-know-about-the-combined-registration-portal-for-azure-mfa-and-self-service-password-reset/) where I explained that the SSPR and MFA settings are very intertwined and how you could tweak the settings to your liking by experimenting with different variations of settings. However, settings are managed in different portals and without comprehensive programmatic control. Let's see what we have today:

We have the legacy multi-factor authentication portal ([https://aka.ms/MFAPortal](https://aka.ms/MFAPortal)), where we can manage the MFA methods and trusted IP's.

![](/assets/images/image-19.png)

Next, we have the [blade in the Azure portal](https://portal.azure.com/#view/Microsoft_AAD_IAM/PasswordResetMenuBlade/~/AuthenticationMethods), where we can configure the SSPR methods.

![](/assets/images/image-20.png)

Lastly, we have the newest kid on the block: the [Authentication methods policy blade](https://portal.azure.com/#view/Microsoft_AAD_IAM/AuthenticationMethodsMenuBlade/~/AdminAuthMethods).

![](/assets/images/image-21.png)

So three portals to configure settings for SSPR and MFA. What's not to like? :)

## What's new?

With the Authentication Methods Policy Convergence preview, Microsoft is consolidating the configuration of MFA and SSPR methods into Authentication Methods Policies. That already has two significant benefits:

1. One portal to rule them all.

3. More flexibility and granularity.

This public preview will bring a few new methods, and some updates to the existing ones:

| Policy | Status | What's new? |
| --- | --- | --- |
| SMS | _Updated_ | You can now manage if users can use SMS for MFA and SSPR. Optionally, you can configure the SMS Sign-in feature. |
| Microsoft Authenticator | _Updated_ | You can now disable the use of One Time Code on the Microsoft Authenticator app. |
| Voice Call | **New** | Enable users to use a phone call to a mobile or office phone for MFA and SSPR. Optionally, you can disable the office phone. |
| Third-party software OATH tokens | **New** | Enable users to use OATH standard-based authenticator apps like Google Authenticator. |
| Email OTP | **New** | Enable users and guests to use an Email one-time passcode for SSPR. |
| Temporary Access Pass | N.A. |   |
| FIDO2 security key | N.A. |   |
| Certificate-based Authentication | N.A. |   |

Admins can now define policies based on user groups instead of having a tenant-wide setting. Next, they can also be more specific in what methods and features they allow in their organization.

For example:

1. Enable SMS for a specific group only.

3. Disable voice and SMS for administrators, which goes hand in hand with [Conditional Access authentication strength (preview)](https://learn.microsoft.com/en-us/azure/active-directory/authentication/concept-authentication-strengths)

5. Enforce the use of the Microsoft Authenticator App and block other authenticator apps like Google Authenticator.

7. Configure different SSPR and MFA methods for specific personas in your organization.

9. Limit the use of the TOTP feature in the Microsoft Authenticator app.

As you can see, this feature brings a whole new set of flexibility.

## Call for action

In January 2024, the legacy multifactor authentication and self-service password reset policies will be deprecated, and you'll manage all authentication methods using the new authentication methods policies.

Good to know: the new Authentication Methods policies are evaluated **_in addition to_** legacy MFA and SSPR policies (both will be evaluated with OR condition).

Therefore, a new control is introduced where you can migrate your existing policies to the new blade at your own pace. By default, all legacy policies are respected, and the new policies are used for authentication only. During migration, you can use the policies for both SSPR and MFA, while still respecting the legacy policies and finally move over to the other side where legacy policies are ignored.

There is new documentation available on how to migrate your existing legacy policies to the new authentication methods policy. [How to migrate to the Authentication methods policy - Azure Active Directory - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/authentication/how-to-authentication-methods-manage)

![](/assets/images/msedge_8SDg3NlJfE.png)

Ensure you have also enabled the combined registration portal for SSPR and MFA before using the new policies. Microsoft should have already enabled this feature, starting Sept. 30th, 2022, but I still see tenants where this is disabled.

![](/assets/images/image-23.png)

## Legacy MFA and SSPR settings

In this private preview, you can disable (almost) all authentication methods from the legacy portal and move over to the new policies.

![](/assets/images/image-22.png)

  
If you are using Hardware OATH tokens on your tenant, **ensure you don’t disable the “Verification code from mobile app or hardware token”** from the legacy MFA portal.

“Verification code from mobile app or hardware token” controls 3 authentication methods:

- Hardware OATH Tokens;

- Verification code from 3rd party authenticator apps like Google Authenticator;

- Verification code as part of the Microsoft Authenticator app.

At the time of writing, the public preview doesn’t include Hardware OATH Token policy support. Customers using Hardware OATH tokens on their tenants must not disable “Verification code from mobile app or hardware token” as this will result in users not being able to use hardware tokens.

For the legacy SSPR portal, only **_Security Questions_** will continue to be managed on the SSPR blade. A control for Security questions is coming soon, but personally I recommend not to use security questions for password reset.

![](/assets/images/image-31.png)

Once you move to "Migrated", the SSPR methods required to reset the password can be changed downward, but not upward. If you want to change it later on, you need to move to "Migration In Progress" again before it accepts changes.

![](/assets/images/image.png)

_"Number of methods chosen is less than number of methods required to reset"_

## Example - Allow 3rd party Authenticator Apps for a specific group only

To show you how this new feature works, we'll see if we can limit the use of a 3rd party app to a specific group only. Assuming that you have enabled this feature now, this is a two-step tutorial:

1. Configure the Third-party software OATH tokens policy.

3. Disable the method on the legacy MFA portal.

We will start with enabling the Third-party software OATH tokens policy, adding the Allow\_Software OATH tokens\_3rdPartyApps group to the policy. Now add your test user to the group.

![](/assets/images/image-24.png)

Debra is a member of my test group.

![](/assets/images/image-28.png)

Next, we need to disable the tenant-wide setting. Uncheck the box **"Verification code from mobile app or hardware token".**

![](/assets/images/image-25.png)

Now, when Debra tries to register for MFA or SSPR, she can select a 3rd party app, while Megan, who is not a member of the group, cannot.

<figure>

![](/assets/images/image-27.png)

<figcaption>

Since Debra is a **member** of the Allow\_Software OATH tokens\_3rdPartyApps group, she can register a third-party app  
  

</figcaption>

</figure>

<figure>

![](/assets/images/image-26.png)

<figcaption>

Megan is **not** part of the Allow\_Software OATH tokens\_3rdPartyApps group, so she can only register the Microsoft Authenticator App

</figcaption>

</figure>

**_Be aware that it can take up to 15 minutes before changes are reflected._**

## Wrap things up

This preview is a first good step into central management of all SSPR and MFA related methods, which is a welcome new feature. The current situation can be confusing, and is not very flexible. Please reach out to the documentation on Microsoft Learn for more guidance:

[How to migrate to the Authentication methods policy - Azure Active Directory - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/authentication/how-to-authentication-methods-manage)  
[Manage authentication methods - Azure Active Directory - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/authentication/concept-authentication-methods-manage)  

Stay tuned for more news and improvements!
