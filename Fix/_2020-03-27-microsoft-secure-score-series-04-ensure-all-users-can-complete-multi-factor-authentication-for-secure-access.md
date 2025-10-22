---
title: "Microsoft Secure Score Series – 04 – Ensure all users can complete multi-factor authentication for secure access"
date: 2020-03-27
categories: 
  - "entra"
  - "secure-score"
  - "security"
tags: 
  - "azure-ad"
  - "identity"
  - "mfa"
  - "registration"
  - "risk"
  - "secure-score"
  - "security-defaults"
coverImage: "door-handle-key-keyhole-279810-scaled.jpg"
---

## Ensure all users can complete multi-factor authentication for secure access

_Multi-factor authentication (MFA) helps protect devices and data that are accessible to these users. Adding more authentication methods, such as the Microsoft Authenticator app or a phone number, increases the level of protection if one factor is compromised._

* * *

In this blog post, we take a look at the different ways to make sure that your users can register for multi-factor authentication. Enabling Multi-Factor Authentication is a no-brainer giving the fact that your identity is your key to almost all your apps and data. When you use Azure AD, enabling Multi-Factor Authentication will stop (almost) all of the malicious actors, trying to steal your credentials.

#### First things first

There a couple of ways to enable Multi-Factor Authentication for your users. Some approaches are eligible for all licenses, some require premium licenses. Before I start I want to point out that the **recommended approach** for enabling Multi-Factor Authentication is using Conditional Access. This gives you the most granularity on when, where and who is prompted for Multi-Factor Authentication. More on that later.

Next, I would recommend enabling the [combined security information registration portal](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) for your administrators. Using this portal, your users can register not only for MFA but also for Self Service Password Reset. Normally, this registration process would take place in two different portals. The new portal is in preview for quite a while now, but I like it much more than the “old” one. The wizard is more intuitive and smoother. You can enable this combined portal trough the **Azure portal -> Azure Active Directory -> User Settings -> User feature previews**

![](/assets/images/image-12.png)

In my previous blog, I talked about enabling Multi-Factor Authentication for your privileged (admin) users. If you are planning on enabling Multi-Factor Authentication for your users, and you have not yet enabled it for your admins, also take a look at [this previous post](https://janbakker.tech/2020/03/11/microsoft-secure-score-series-02-require-mfa-for-administrative-roles/).

#### Security Defaults

Enabling the Security Defaults setting is the easiest way to enable Multi-Factor Authentication for your organization. Security defaults contain preconfigured security settings for common attacks. If you enabled this for your tenant, Multi-Factor Authentication is enabled for all your users. Users have 14 days to register for Multi-Factor Authentication by using the Microsoft Authenticator app. After the 14 days have passed, the user won't be able to sign in until Multi-Factor Authentication registration is finished. Enabling this setting is most common to smaller companies to enable this basic layer of security without having to worry about a complex implementation. This is perfect for smaller organizations.

The downside of this setting is that you cannot make exceptions. A good thing to point out here is that also legacy authentication is blocked. Legacy protocols such as IMAP and POP are disabled. If you want more granularity, take a look at Conditional Access. More on that later.

###### How to enable Security Defaults?

To enable Security Defaults for your tenant, go to the Azure portal: [https://portal.azure.com](https://portal.azure.com)

Browse to **_Azure Active Directory -> Properties -> Manage Security Defaults_**

![](/assets/images/image-16.png)

If your tenant was created on or after October 22nd, 2019, it’s possible you are experiencing the new secure-by-default behavior and already have security defaults enabled in your tenant. Also keep in mind, that if you have any conditional access or identity protection policies configured, you cannot enable Security Defaults.

Security Defaults can be enabled regardless of the version of Azure AD. So in the next chapters, keep in mind that enabling Security Defaults is always an option.

#### Azure Multi-Factor Authentication per user

Multi-Factor Authentication per user can be enabled by either using the Office 365 admin portal or through the Azure portal. Both will open up the (super ugly ;) ) MFA settings page, where you can enable Multi-Factor Authentication per user, or in bulk.

- ![](/assets/images/msedge_quj7rG3v7s-1024x268.png)
    
- ![](/assets/images/msedge_NQKgwJ1rj5-1024x259.png)
    

When users are enabled individually, they perform two-step verification **each time** they sign in (with some exceptions, such as when they sign in from trusted IP addresses or when the _remembered devices_ feature is turned on).  As already mentioned earlier, this is a labor-intensive way and a non-granular way of enabling Multi-Factor Authentication. Using this portal, you have 3 options for your users:

- **Disabled**: The default state for a new user not enrolled in Azure MFA.
- **Enabled**: The user has been enrolled in Azure MFA but has not registered. They receive a prompt to register the next time they sign in.
- **Forced**: The user has been enrolled and has completed the registration process for Azure MFA.

This can also be done with Powershell. For example, here is the script to enable Multi-Factor Authentication for a single user:

```
Import-Module MSOnline
 Connect-MsolServic
 $st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
 $st.RelyingParty = "*"
 $st.State = "Enabled"
 $sta = @($st)
 Set-MsolUser -UserPrincipalName username@contoso.com -StrongAuthenticationRequirements $sta
```

You can also use Powershell to enable Multi-Factor Authentication for all your users, and of course, use it to disable if needed. If you already use user-based MFA, Powershell can help you with the conversion to Conditional Access based Azure Multi-Factor Authentication. [Learn more.](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-userstates#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)

#### Conditional Access

Next, we take a look at Conditional Access (requires Premium P1 license). Using this feature, you can challenge your users for Multi-Factor Authentication when they access cloud applications. You can specify certain conditions such as location, device type or OS.

![Conceptual Conditional Access process flow](/assets/images/conditional-access-overview-how-it-works.png)

You can build multiple policies for different scenarios. Today, we create a policy to enforce users to register for Multi-Factor Authentication. When a user is successfully registered, your policies determine when a user is prompted for MFA when they log in.

In the Azure portal Conditional Access page

1. Select **+New policy**
2. Users and groups > Include all users ([exclude service - and emergency accounts](https://janbakker.tech/2020/03/11/microsoft-secure-score-series-02-require-mfa-for-administrative-roles/))
3. Cloud apps > **All cloud apps**
4. Grant > Require multi-factor authentication (and nothing else)
5. Enable policy > **On**
6. **Create**

If you just want your users to register for MFA, and not prompt them for any cloud app (not recommended), you can create a group and add all your users to it. Once they registered, remove them from that group. You can use the Graph API, for example, to see which users already registered for MFA.

```
GET https://graph.microsoft.com/beta/reports/credentialUserRegistrationDetails?$filter=isMfaRegistered eq true
```

#### Azure AD Identity Protection

If your users have Premium P2 licenses, you can use the MFA registration policy in Azure AD Identity Protection. Azure AD Identity Protection protects your users by prompting for MFA on a risky sign-in. Also, you can set up remediation policies in case your users have a medium or high user risk. You can then automatically block the user's account, or force a password reset using the Self Service Password Reset tool.

![Simplified risk structure..png](/assets/images/large)

You can enable the MFA registration policy in order to prompt your user for registration. Just like Security Defaults, users have 14 days to register for Multi-Factor Authentication. After the 14 days have passed, the user won't be able to sign in until Multi-Factor Authentication registration is finished. Users can set up more than one method.

In the Azure portal, configure the MFA registration policy by going to the [MFA registration page](https://go.microsoft.com/fwlink/?linkid=2094926).

1. Under Assignments > Users - Choose All users or choose Select individuals and groups if limiting your rollout
2. Under Controls - Ensure the checkbox Require Azure MFA registration is checked and choose **Select**
3. Enforce policy > **On**
4. **Save**

![](/assets/images/image-20.png)

#### Both of best worlds

![](/assets/images/image-22-1024x508.png)

You can use the user's sign-in risk from Azure AD Identity Protection as a condition in Conditional Access. This way you can add more granularity to your MFA policies. Users will only get prompted for MFA on unusual or suspicious events.

#### Manual registration

This one is often overlooked, but you can also simply **ask** your users to register for MFA ;) Just so you know this is an option. This way, your users are not interrupted in their work and can register when it fits their schedule. Users can browse to [https://aka.ms/mfasetup](https://aka.ms/mfasetup) and just follow the wizard.

![](/assets/images/image-21.png)

#### Wrap things up

As you can see, there are many ways to get your users registered for MFA. Don't wait too long if you have not yet planned your strategy. For more details on this, take a look at the [Microsoft documentation](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-getstarted). Stay tuned for more articles in this Secure Score series.

Stay safe out there!
