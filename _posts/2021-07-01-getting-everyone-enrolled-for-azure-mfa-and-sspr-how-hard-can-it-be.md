---
title: "Getting everyone enrolled for Azure MFA and SSPR. How hard can it be?"
date: 2021-07-01
categories: 
  - "entra"
  - "power-platform"
  - "security"
image: "/assets/images/pexels-anna-shvets-5257496-1.jpg"
---

I've done quite some Azure MFA implementations over the past few years, and none of them were the same. But one thing that was often the same is the way Azure MFA (or SSPR) was implemented: in **two** steps.

First, you want to get your users enrolled ASAP. Once everybody (or at least the vast majority) is enrolled, you can enforce Azure MFA, so that your identities are better protected against phishing. It is pretty straightforward.

![](/assets/images/image.png)

## Registration methods

There are a couple of ways to get your users registered for Azure MFA:

- **Turn on Security Defaults.** Next to registration, this would also enforce MFA (when needed). This is the easiest but least flexible way.
- **The "pretty please method".** Simply ask your users to register using [https://aka.ms/mfasetup](https://aka.ms/mfasetup) or [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).
- **SSPR registration policy**. Since the registration of MFA and SSPR can be combined these days, you could use this policy to get your users registered at the next sign-in.
- **Identity Protection MFA registration policy**. Same experience as the Security Defaults method, but you need to have Azure premium P2.
- **Conditional Access.** By creating a policy to enforce MFA, users that did not register will be prompted for registration at the next sign-in.
- **Provisioning.** Of course, you could also pre-populate your MFA methods such as phone number, and then use one of the other methods on top of that to complete the registration with more secure methods such as the Microsoft Authenticator app.

Did you know that every method comes with a different experience for your end-users? For example: if you use the SSPR registration method, users can skip the wizard whenever they want. If you use Security Defaults, or Identity Protection policy, users can also skip but only for 14 days. Read all about it in [this blog post](https://janbakker.tech/what-admins-should-know-about-the-combined-registration-portal-for-azure-mfa-and-self-service-password-reset/) that I wrote earlier.

## The challenge

Now, assuming that you pick one of the registration policies, a good thing to mention is that this will only kick in whit **interactive** sign-ins. So, when you have enabled the Keep Me Signed In (KMSI) option, it can take up to 90 days before someone has to re-enter their credentials, especially when you use modern mobile and desktop clients. That means they will _never_ get prompted for MFA or SSPR registration.

So I asked my beloved followers on Twitter how to fix this, and the opinions where quite divided.

![](/assets/images/image-1.png)

One option that stood out was option B: enforce MFA. This would be in fact the best option from a security perspective, but could also have a high impact on your users. So let's consider options C and D. They both do the same, revoking the refresh token so that the user must reauthenticate to get a new access token.

## Sign-in frequency

You could enable a Conditional Access policy to set the [sign-in frequency](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime) for a specific application, Teams for example.

![](/assets/images/image-5.png)

This will force your users to re-authenticate, so that the registration process is kicked off.

## Revoke refresh token

This option is a little more aggressive, but will revoke your users' refresh token, so that the need to re-authenticate to any app that they've signed in to before. One of the easiest way to do this, is using the Azure portal.

![](/assets/images/image-7.png)

But you can also use the Graph API or PowerShell for this.

```
POST https://graph.microsoft.com/beta/users/rachel.green@centralperk.org/revokeSignInSessions
```

```
PS C:\> Revoke-AzureADUserAllRefreshToken -ObjectId "a1d91a49-70c6-4d1d-a80a-b74c820a9a33"
```

![](/assets/images/image-8.png)

In the audit logs, you can actually see that the value is changed.

![](/assets/images/image-10.png)

## Power Automate for the win

If you are not that good with PowerShell (like me), here is a great tip. You can use Power Automate for this as well. And the best part: it's out of the box. No need for app registration, API permissions, or HTTP requests. Just look for the **Azure AD module** and the Refresh tokens action. The easiest way to do this in bulk is by selecting an Azure AD group, and using the _apply to each_ action to revoke the tokens for all the users in the group.

![](/assets/images/image-9.png)

## Wrap things up

Doing a smooth MFA or SSPR roll-out works best when all (or most) of your users are properly registered. As you might know by now, this can be quite challenging. I hope that you find some value in this post that you can use to protect your organization, or the clients that you work with. Keep those users secure and happy!

**Read more:**

[Revoke-AzureADUserAllRefreshToken (AzureAD) | Microsoft Docs](https://docs.microsoft.com/en-us/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)  
[user: revokeSignInSessions - Microsoft Graph v1.0 | Microsoft Docs](https://docs.microsoft.com/en-us/graph/api/user-revokesigninsessions?view=graph-rest-1.0&tabs=http)  
[Configure authentication session management - Azure Active Directory | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)  
[Hoe Multi-Factor Authenticatie (MFA) activeren in Microsoft Office 365? (365tips.be)](https://365tips.be/hoe-multi-factor-authenticatie-mfa-activeren-in-microsoft-office-365/) (Dutch only)

Stay safe!
