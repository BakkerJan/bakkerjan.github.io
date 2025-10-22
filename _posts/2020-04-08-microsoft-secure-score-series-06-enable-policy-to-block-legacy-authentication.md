---
title: "Microsoft Secure Score Series – 06 – Enable policy to block legacy authentication"
date: 2020-04-08
categories: 
  - "entra"
  - "secure-score"
  - "security"
tags: 
  - "authentication"
  - "block"
  - "conditional-access"
  - "imap"
  - "legacy"
  - "office-365"
  - "pop3"
  - "secure"
  - "secure-score"
  - "security-defaults"
image: "/assets/images/person-holding-black-cassette-tape-1698065-scaled.jpg"
---

## Enable policy to block legacy authentication

_Today, most compromising sign-in attempts come from legacy authentication. Older office clients such as Office 2010 don’t support modern authentication and use legacy protocols such as IMAP, SMTP, and POP3. Legacy authentication does not support multi-factor authentication (MFA). Even if an MFA policy is configured in your environment, bad actors can bypass these enforcements through legacy protocols._

* * *

In this blog post, we take a look at legacy authentication and how to block it on your tenant. Legacy protocols are not compatible with modern authentication such as Multi-factor Authentication. So, if you enabled MFA for user users (good job!), that does not mean that you can lean back and relax. Hackers can still try to brute force passwords using older protocols, and older clients can still connect using these protocols. For this blog post, I assume that you already [enabled modern authentication](https://docs.microsoft.com/en-us/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication?view=o365-worldwide#enable-modern-authentication-for-your-organization) for your tenant. We are focussing on the clients that **don't** support modern authentication. **Enabling** MFA often goes hand in hand with **disabling** legacy protocols. So MFA will lock up the front door, disabling legacy will shut the backdoor.

![](/assets/images/f63ed4353608ac2c6080fdafedd60865.png)

To start of, let's take a look at the definition of legacy protocols. Here's the list of all the options that are considered legacy:

- Authenticated SMTP - Used by POP and IMAP client's to send email messages.
- Autodiscover - Used by Outlook and EAS clients to find and connect to mailboxes in Exchange Online.
- Exchange Online PowerShell - Used to connect to Exchange Online with remote PowerShell. If you block Basic authentication for Exchange Online PowerShell, you need to use the Exchange Online PowerShell Module to connect.
- Exchange Web Services (EWS) - A programming interface that's used by Outlook, Outlook for Mac, and third-party apps.
- IMAP4 - Used by IMAP email clients.
- MAPI over HTTP (MAPI/HTTP) - Used by Outlook 2010 and later.
- Offline Address Book (OAB) - A copy of address list collections that are downloaded and used by Outlook.
- Outlook Anywhere (RPC over HTTP) - Used by Outlook 2016 and earlier.
- Outlook Service - Used by the Mail and Calendar app for Windows 10.
- POP3 - Used by POP email clients.
- Reporting Web Services - Used to retrieve report data in Exchange Online.
- Other clients - Other protocols identified as utilizing legacy authentication.

Source: [https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/block-legacy-authentication#legacy-authentication-protocols](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/block-legacy-authentication#legacy-authentication-protocols)

When you go through this list, you can imagine what impact this might have on your organization when you start blocking legacy protocols. Users might use all kinds of clients to get to their email, calendars, and contacts. And don't forget about (third-party) apps that connect to your Azure tenant using basic authentication, such as monitoring & backup tools.

#### Who's still using legacy?

To get an idea of who is still using legacy authentication, you can do multiple measurements. This way you can calculate the impact that it might have, and take the proper actions. First, you can use the **sign-in logs in Azure Active Directory**. Here you can add a filter (client) and select all the legacy protocols.

![](/assets/images/image-12.png)

Next, you can use the Sign-ins using **Legacy Authentication workbook**. This give's you an equivalent overview of your legacy clients.

![](/assets/images/image-13.png)

The last one is part of the solution. We're creating a **Conditional access** policy, but instead of enabling it right away, we use the **report-only mode**. This way you can see what would have happened when you enabled it, but the users are not impacted.

To do this, head over to Conditional Acces and create a new policy, select **all users** and **all cloud apps**. Exclude users, groups or apps if needed. At the conditions tab, make sure you select **Client apps> Mobile apps and desktop** clients and **Other clients**.

In the acces control tab, select **Block**, and make sure you select the **Report-only** toggle.

![](/assets/images/image-14.png)

Now, just as the first option, you can go through your sign-ins and see what the impact on your users is. As you can see in the screenshot, legacy users can still connect, but if you enable the policy, they will be blocked.

![](/assets/images/image-15-1024x521.png)

To start blocking legacy authentication, just switch Report-only to **Enable**.

![](/assets/images/image-16.png)

#### Security Defaults

For smaller organizations, using Security Defaults might be the best solution to block legacy authentication. This will protect your users with Multi-factor Authentication and disables legacy authentication in one single click.  

![](/assets/images/image-17.png)

#### Go modern

Encourage your users to only use clients that support modern authentication. Almost any [Microsoft first-party apps support modern authentication](https://docs.microsoft.com/en-us/office365/enterprise/office-365-client-support-modern-authentication), but also some 3rd party apps or native clients on mobile devices support this. I would suggest you always use Microsoft first-party apps because you can protect them with Intune App Protection.

![Microsoft Outlook voor iOS en Android](/assets/images/RE2Vb9Z)

#### Wrap things up

Microsoft's vision on this is crystal clear: legacy authentication is going to be disabled on all tenants. This [was stated a few times](https://techcommunity.microsoft.com/t5/exchange-team-blog/improving-security-together/ba-p/805892) now. This month an [update stated](https://techcommunity.microsoft.com/t5/exchange-team-blog/basic-authentication-and-exchange-online-april-2020-update/ba-p/1275508) that this will be postponed due to the COVID-19 crisis. This will give organizations a little bit more time to prepare for this change. Unfortunately, hackers will not rest, not even in times of crisis. Especially now organizations have less focus on security, this is an even bigger risk. So the sooner you get rid of legacy authentication, the better.

#### Wrap things up

Disabling legacy authentication can have a large impact on your users. So, try to start small. As descrbed earlier, start with the report only mode. On top of that you can build a second policy for a small pilot group to disabled legacy authentication for real. Add more and more groups to this policy to policy to move forward. Inform your users about the alternatives, and encourage them to update their clients.
