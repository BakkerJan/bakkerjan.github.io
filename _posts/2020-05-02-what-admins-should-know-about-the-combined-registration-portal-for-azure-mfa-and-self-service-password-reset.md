---
title: "What admins should know about the combined registration portal for Azure MFA and Self Service Password Reset"
date: 2020-05-02
categories: 
  - "entra"
  - "security"
tags: 
  - "authentication"
  - "azure-ad"
  - "combined"
  - "mfa"
  - "password-reset"
  - "portal"
  - "self-service"
  - "sspr"
image: "/assets/images/combined-registration-portal.png"
---

#### **This post is outdated.** There is a [new way](https://janbakker.tech/goodbye-legacy-sspr-and-mfa-settings-hello-authentication-methods-policies/) to manage authentication methods

[Learn more](https://janbakker.tech/goodbye-legacy-sspr-and-mfa-settings-hello-authentication-methods-policies/)

The (long) title pretty much reveals the purpose of this blog post. This one was on my to-do list for a while now, and now the [combined registration portal is General Available](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/combined-mfa-and-password-reset-registration-is-now-generally/ba-p/1257355), the time was there. In my previous MFA-related blogs, I always encouraged my readers to turn on the combined registration portal, even when it was in public preview. But if you start using this portal, there are quite some settings that can change the user experience of the registration. And that's mainly what this blog post is about. Let's see how each setting reflects to the end user.

#### What is the combined registration portal?

Until now, the registration for MFA and Self Service Password in Azure AD needed to be done in two different portals. In most cases, users had to fill in the same information in two different places.

For MFA: [https://aka.ms/mfasetup](https://aka.ms/mfasetup)

![](/assets/images/msedge_LoiIqqlufq.png)

For SSPR: [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup)

![](/assets/images/image-6.png)

Once the combined registration portal is enabled users can either use [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) or [https://aka.ms/mfasetup](https://aka.ms/mfasetup) to access the portal and register for both MFA and SSPR in one single flow. Even though MFA/SSPR Combined Registration is now generally available, Microsoft is not automatically switching everyone over to the new user experience. You can control this yourself.  

![](/assets/images/image-7.png)

#### First things first

Now, before users register for MFA and SSPR you should configure the settings first. There are a couple of steps that you need to do:

- Enable the combined registration portal for your users

- Pick a roll-out strategy. How are your users going to register?

I covered both these subjects in a [previous blog post](https://janbakker.tech/2020/03/27/microsoft-secure-score-series-04-ensure-all-users-can-complete-multi-factor-authentication-for-secure-access/).

- Enable and configure Self Service Password Reset - Covered in [this blog post](https://janbakker.tech/2020/03/30/microsoft-secure-score-series-05-enable-self-service-password-reset/)

* * *

So I assume that you read my previous blogs first and that you have a clear understanding of all the steps that you need to take before you can roll out MFA and SSPR to your users. But wait, there is more......

## End-user experience

So let's talk about the end-user experience in the combined registration portal. Some organizations are having a hard time getting everybody enabled for MFA (and SSPR), and I am honest with you guys: it can be quite tricky sometimes. Although the new combined registration wizard is pretty smooth, there is always a chance users get stuck somehow.

That's why I suggest you should offer **as many methods as possible** for the user to register. What do I mean by that? If you, for example, enable [Security Defaults](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) on your tenant, users can only register by using the Authenticator App. When all of your users got a brand new iPhone with plenty of storage, that would not be an issue. But there are a lot of users out there with old hardware with even older software on it. Hardware that's not even capable of installing the Authenticator app.

So that's why you should let them pick from multiple methods. "_But Jan, SMS is not safe, it can be easily manipulated these days_!" True that is, but there is one thing you have to keep in mind here:

![](/assets/images/image-9.png)

So onboarding with just SMS or phone call is fine. You can add more methods later and let your users get used to MFA first. That being said, I want to show you how you can "configure" the end-user experience of the registration portal for the users. I used air quotes there because there is not a single place to that. You can configure these options in multiple places, and each combination will have different outcomes to the end-user.

#### Authentication and verification methods

There are basically two places where you can configure the authentication/verification methods. Both sections have an impact on the registration wizard. The first one can be found under the Multi-factor Authentication service settings in the Azure portal. Go to **Azure Active Directory -> Security -> MFA** and click on **Additional cloud-based MFA settings** in the _Getting started_ page. Or use this URL: [https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx)

![](/assets/images/image-13.png)

![](/assets/images/image-14.png)

**A heads up: when you edit these settings it takes some time before they get reflected in the combined portal. In my tenant, it takes 10-15 minutes after each change. So take your time when you test this out.**

The second location is the password reset section in the Azure Portal. You can configure these settings separately, but they both reflect in the same portal. Go to **Azure Active Directory -> Password reset -> Authentication methods**

![](/assets/images/image-10.png)

#### Registration policy

Combined registration respects both Multi-Factor Authentication and SSPR policies if both are enabled for your tenant. These policies control whether a user is interrupted for registration during sign-in and which methods are available for registration.

Depending on your enrollment strategy, your users are prompted to register. In [this blog](https://janbakker.tech/2020/03/27/microsoft-secure-score-series-04-ensure-all-users-can-complete-multi-factor-authentication-for-secure-access/) I talked about the different ways to do that. Take a look at this flowchart to understand what methods are shown to your users when you enabled the registration policies.

<figure>

![Combined security info flowchart](/assets/images/combined-security-info-flow-chart.png)

<figcaption>

Source: [https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-registration-mfa-sspr-combined](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

</figcaption>

</figure>

Good to know: When registration is enforced, users are shown the minimum number of methods needed to be compliant with both Multi-Factor Authentication and SSPR policies, **from most to least secure**. If you have both Multi-Factor Authentication and SSPR enabled, Microsoft recommends that you enforce Multi-Factor Authentication registration.

If you enabled either Security Defaults or Azure Identity Protection MFA registration policy, users can skip/postpone the registration for 14 days.. Note that MFA per user and MFA by Conditional Access doesn't offer the 14 days grace period. If you only use the SSPR registration policy, users can skip the wizard. (interrupt mode)

<figure>

![](/assets/images/image-16.png)

<figure>

![](/assets/images/msedge_Fk4BhymxRM-1.png)

<figcaption>

Security Defaults / Identity Protection

</figcaption>

</figure>

![](/assets/images/msedge_EOShXqebpU.png)

</figure>

#### Mix 'n match

So let's make some configurations in both MFA and SSPR and see how this reflects in your portal. This gives you an idea of how this works and how the settings reflect in the portal.

<figure>

<figure>

![](/assets/images/Scenario1-1024x613.png)

<figcaption>

Multi-factor authentication registration policy: **Enabled**  
Self Service password registration policy: **Disabled**

</figcaption>

</figure>

<figure>

![](/assets/images/Scenario2-1024x613.png)

<figcaption>

Multi-factor authentication registration policy: **Enabled**  
Self Service password registration policy: **Disabled**

</figcaption>

</figure>

<figure>

![](/assets/images/Scenario3-1024x613.png)

<figcaption>

Multi-factor authentication registration policy: **Enabled**  
Self Service password registration policy: **Disabled**

</figcaption>

</figure>

<figure>

![](/assets/images/Scenario4-1024x910.png)

<figcaption>

Multi-factor authentication registration policy: **Enabled**  
Self Service password registration policy: **Disabled**

</figcaption>

</figure>

<figure>

![](/assets/images/Scenario5-1024x957.png)

<figcaption>

Multi-factor authentication registration policy: **Disabled**  
Self Service password registration policy: **Enabled**

</figcaption>

</figure>

<figure>

![](/assets/images/Scenario6-1024x957.png)

<figcaption>

Multi-factor authentication registration policy: **Enabled**  
Self Service password registration policy: **Disabled**

</figcaption>

</figure>

<figure>

![](/assets/images/Scenario7-1024x957.png)

<figcaption>

Multi-factor authentication registration policy: **Enabled**  
Self Service password registration policy: **Disabled**  
Note that the user can choose to pick a different method

</figcaption>

</figure>

<figure>

![](/assets/images/Scenario8-1-1024x907.png)

<figcaption>

Multi-factor authentication registration policy: **Enabled**  
Self Service password registration policy: **Disabled**

</figcaption>

</figure>

</figure>

The last thing to know is the verification method "**Verification code from mobile app or hardware token**" will add another field in the wizard where users can choose for 3rd party authenticator apps such as Google Authenticator.

![](/assets/images/image-17.png)

#### Let's wrap up

As you can see there are countless options here. I hope this blog will give you some insight so that you know what the new combined portal is capable of. It is possible that the screenshots provided in this blog are not the same as you see in your tenant. That might be due different configurations, policies, or the fact that use either MFA, SPPR or both. Also, the UI might change over time.

I've seen some use cases where organizations used the SSPR registration policy to onboard the users for Multi-Factor Authentication. This way you can prompt your users to register their methods, but users can skip the wizard. This way users can take more time to register, unlike the 14 days grace period that comes with the Identity Protection registration wizard.

Either way, I have been struggling with this myself, so I wanted to show you some examples. Hopefully, this makes it easier to understand how this works. Take your time to figure this out.

Also, I want to mention that with the use of the new portal you can also register your FIDO2 security key's and enable SMS sign-in. Both methods provide a passwordless experience for your users. Stay tuned for some blogs about that ;)

![](/assets/images/image-18.png)

Stay safe!
