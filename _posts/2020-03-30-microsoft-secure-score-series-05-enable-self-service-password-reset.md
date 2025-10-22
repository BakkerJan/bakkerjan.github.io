---
title: "Microsoft Secure Score Series – 05 – Enable self-service password reset"
date: 2020-03-30
categories: 
  - "entra"
  - "secure-score"
  - "security"
tags: 
  - "automation"
  - "azure"
  - "password-reset"
  - "passwords"
  - "portal"
  - "self-service"
  - "writeback"
coverImage: "man-in-black-suit-covering-his-face-with-two-hands-3778964-scaled.jpg"
---

## Enable self-service password reset

_With self-service password reset in Azure AD, users no longer need to engage helpdesk to reset passwords. This feature works well with Azure AD dynamically banned passwords, which prevents easily guessable passwords from being used._

* * *

In this blog post, we are going to take a look at Self Service Password Reset in Azure AD. Self Service Password Reset allows your users to quickly unblock their account without the help of IT staff or helpdesk. For readability, I use **SSPR** for the rest of the article.

#### The struggle with passwords

Users are struggling with their corporate passwords for ages. In fact, most handled tickets at helpdesks are regarding passwords. They are often forgotten or mistyped with account lockout as a result. Users' passwords are being reset to passwords like _Welcome01_ or _Winter2020_ and users are allowed to change this to an even weaker password, mostly their "standard" password, with a rotating variable like a number.

Most companies have password policies that are forcing the users to change the password every 90 days or so. And giving the fact that users already have so many passwords to remember, the reuse passwords, or add variables to that.

![](/assets/images/man-in-black-suit-covering-his-face-with-two-hands-3778964-scaled.jpg)

In my [previous post](https://janbakker.tech/2020/03/27/microsoft-secure-score-series-04-ensure-all-users-can-complete-multi-factor-authentication-for-secure-access/), I showed how you can enable Multi-Factor Authentication for all your users. Users can register for Self Service Password Reset using the same portal now. Using the [combined registration portal](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-registration-mfa-sspr-combined), users can register for both MFA and SSPR in one single flow. I strongly suggest that you enable this experience for your users.

#### First things first

SSPR for cloud users requires one of the following licenses: Azure AD Premium P1 or P2, Microsoft 365 Business, or Office 365. If you have a hybrid environment, you also need password writeback into your on-premises AD. In this case, you'll need Azure AD Premium P1 or P2 or Microsoft 365 Business.

#### SSPR and Azure AD Hybrid environments

In hybrid environments, SSPR requires the option "password writeback" to be enabled on Azure AD Connect. This is needed so that Azure AD Connect can sync back the passwords into the on-premises Active Directory.

1. Sign in to your Azure AD Connect server and start the **Azure AD Connect** configuration wizard.
2. On the **Welcome** page, select **Configure**.
3. On the **Additional tasks** page, select **Customize synchronization options**, and then select **Next**.
4. On the **Connect to Azure AD** page, enter a global administrator credential for your Azure tenant, and then select **Next**.
5. On the **Connect directories** and **Domain/OU** filtering pages, select **Next**.
6. On the **Optional features** page, select the box next to **Password writeback** and select **Next**.

![Configure Azure AD Connect for password writeback](/assets/images/enable-password-writeback.png)

Next, make sure the account you use in AD Connect is having the right permissions. If you're not sure which account is currently in use, open Azure AD Connect and select the **View current configuration** option. Because Azure AD Connect is syncing back passwords, it also needs to update a couple of attributes in the AD object. [More details.](https://docs.microsoft.com/en-us/azure/active-directory/authentication/tutorial-enable-sspr-writeback#configure-account-permissions-for-azure-ad-connect)

Now we have enabled password writeback, we need to configure the setting in the Azure portal as well. Head over to your Azure portal, and go to **Azure Active Directory -> Password reset -> On-premises integration**

![](/assets/images/image-25.png)

If you turn on both options, your users are able to reset their passwords, but also unlock their account without resetting the password. If you set _**Allow users to unlock accounts without resetting their password**_ to No, users can only unlock their accounts by resetting their password.

#### Enable Self Service Password Reset

Now that we have everything in place, lets head over to the configuration of SSPR itself. Let's go over all the steps and the options that can be configured.

Go to your Azure portal and browse to **Azure Active Directory -> Password reset**. Choose whether you want to enable SSPR for all of your users, or just for a specific group of users.

![](/assets/images/image-26.png)

#### Authentication methods

Next, we are configuring the authentication methods. Here's where things are getting a bit messy. It can be a bit confusing, so bear with me.

![](/assets/images/msedge_noL8XAOo8e.png)

First, you should enable the [converged registration portal](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-registration-mfa-sspr-converged), as discussed earlier in this blog. This will give you and your users the best experience in both MFA and SSPR registration because this can be handled in **one** flow. Take a look at the note here from the Microsoft docs page regarding the use of the Authenticator app.

![](/assets/images/image-27.png)

So, when you play around with the different authentication methods, you'll need to find the settings that work best for you. I totally depends on a couple of related things:

- Did your users already register for Multi-Factor Authentication?
- What methods did you enable for Multi-Factor Authentication?
- What methods did you enable for Self Service Password Reset?
- Did you enable the combined registration portal for your users, or do you prefer the classic portal(s)?
- Do you want a 1 or 2 gates policy in order to reset the passwords of your users?

If you have not enabled the new experience, your users have to register in two places. ( [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) and [https://aka.ms/mfasetup](https://aka.ms/mfasetup)) First, for MFA, it would look like this:

![](/assets/images/msedge_OYNpjLDg1X.png)

The number of methods available depends on the settings in the MFA service settings page. Are you still with me? ;)

![](/assets/images/image-28.png)

The SSPR "classic" portal will look like this:

![](/assets/images/msedge_1Xu3Dn1B07.png)

As stated in the Microsoft note earlier, you cannot register the authenticator app in the current portal. That's why you need to add 2 more methods on top of the authenticator when you use a 2-gates SSPR policy.

If this same user had access to the new registration portal, using [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo), the experience will look like this:

![](/assets/images/image-29.png)

In the second method, the users can choose the different available methods, depending on what you enabled in the settings.

![](/assets/images/image-31.png)

If your users already registered for MFA using the new experience, once you enable SSPR for them, users are eligible for SSPR right away (as long as app notifications and codes are enabled for SSPR) Also take a look at [this excellent blog](https://c7solutions.com/2019/08/mfa-and-end-user-impacts). This will give you more insight into the user experience.

#### Registration

You can either configure to prompt the user for registration or let users manually register via [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) (classic) or [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) (combined). You can configure to let users re-confirm their authentication methods periodically. Set this value to 0 to disable this.

![](/assets/images/msedge_mDyBwG6aSb.png)

#### Notifications

You can configure to inform the users whenever their password is reset or to inform all your admins when an admin password is reset. Set this to your needs.

![](/assets/images/image-32.png)

#### Customization

It is recommended to set up a custom email or helpdesk URL, so your users can read up any documentation or FAQ items regarding password reset. This URL will show during the password reset action as "Contact your administrator". If you provide an email address, it will turn into a _mailto:_ link that will be sent to the email address you specify.

```
I noticed that this URL is not shown right away. When you try a method, after a couple of seconds this button will show up. 
```

- ![](/assets/images/image-33.png)
    
- ![](/assets/images/msedge_wVsH7ps36Z-1024x754.png)
    

#### End-user experience

Now we have everything in place, let's take a look a the user experience. Although my user is eligible to use the new experience, the password reset portal itself still has a very classic look and feel. To reset a password, the users can either go to [https://aka.ms/sspr](https://aka.ms/sspr) or click the "_can't access your account_" button and choose "work or school account" at the log-in screen.

- ![](/assets/images/image-34-1024x850.png)
    
- ![](/assets/images/msedge_RZndqdvjWb-1024x855.png)
    

Next, the user is prompted with all the available methods to recover the password.

![](/assets/images/msedge_HTZ9RPfHXC.png)

When both verification steps are done, the user can enter a new password.

![](/assets/images/msedge_ZtreoxkBYb.png)

#### Reset the password from your managed devices

There are some limitations, but you can integrate the password reset tool with your domain joined, managed clients. For more detail, take a look a the **[Microsoft docs](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-sspr-windows).** One of the easiest ways to enable this is through the registry. The password reset option will become visible from the login screen.

`HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount`

- `"AllowPasswordReset"=dword:00000001`

#### But wait, there's more!

If you use the combined registration portal, you can control access to this portal using Conditional Access. This way, you can secure access by only allowing this action from managed devices or a trusted (office) location for example. Simply create a Conditional Access policy and select **User actions** in the _Cloud app or actions_ tab. Next, choose your conditions. In this example, I block access to the registration portal for any location, except for my trusted locations. Check out my **[earlier blog post](https://janbakker.tech/2020/02/22/require-trusted-location-for-mfa-and-sspr-registration/)** to learn more.

- ![](/assets/images/image-35.png)
    
- ![](/assets/images/msedge_95nvZpqgTd-1-1024x958.png)
    
- ![](/assets/images/msedge_P8gjE3rFyr-779x1024.png)
    

#### Wrap things up

When we talk about MFA and SSPR, a lot of URL's are involved. I created this small overview with all the URL's involved and disciessud in this blogpost.

| Portal | URL |  |
| --- | --- | --- |
| MFA registration portal (classic) | [https://aka.ms/mfasetup](https://aka.ms/mfasetup) |  |
| SSPR registration portal (classic) | [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) |  |
| Combined registration portal for MFA and SSPR | [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) |  |
| Password reset portal | [https://aka.ms/sspr](https://aka.ms/sspr) |  |

Did you enable and configured both MFA and SSPR? Take a look at the flowchart to understand how what the user experience is like.

![Combined security info flowchart](/assets/images/combined-security-info-flow-chart.png)

If you have both Multi-Factor Authentication and SSPR enabled, I recommend that you enforce **Multi-Factor Authentication registration.**

That's it for now guys! If you have any questions, just drop me an email or post a comment. But most important:

Stay safe out there!
