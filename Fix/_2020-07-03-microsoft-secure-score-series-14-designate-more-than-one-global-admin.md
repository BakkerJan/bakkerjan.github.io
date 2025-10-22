---
title: "Microsoft Secure Score Series – 14 – Designate more than one global admin"
date: 2020-07-03
categories: 
  - "entra"
  - "secure-score"
  - "security"
coverImage: "red-toilet-paper-on-white-ceramic-toilet-3964138-1.jpg"
---

## Designate more than one global admin

```
Having more than one global administrator helps if you are unable to fulfill the needs or obligations of your organization. It's important to have a delegate or an emergency account someone from your team can access if necessary. It also allows admins the ability to monitor each other for signs of a breach.
```

Having too many global admins is no good. But having **_only one_** global admin is even worse. Let's talk about global admin and break glass emergency accounts.

## What are global admins?

What is a global administrator? From the [Microsoft documentation](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-assign-admin-roles): _Users who are assigned to the Global administrator role can read and modify every administrative setting in your Azure AD organization. By default, the person who signs up for an Azure subscription is assigned the Global administrator role for the Azure AD organization. Only Global administrators and Privileged Role administrators can delegate administrator roles._

Next to that, if you are a Global Administrator in Azure AD, you can assign yourself [access to all Azure subscriptions](https://docs.microsoft.com/en-us/azure/role-based-access-control/elevate-access-global-admin) and management groups in your directory. In other words: global admins have all the rights or can gain all the rights within your Azure tenant.

## Best practices

There are a lot of best or common practices when it comes to global administrators or admin accounts in general. To name a few:

- You should have only 2-4 global admin accounts in total
- Separate regular user accounts from admin accounts. Don't mix them up.
- Global admins should have strong passwords and MFA must be enabled (and enforced)
- Use the principle of least privilege for your other admin accounts.
- Don't use your federated domain for global admins. Use the @tenant.onmicrosoft.com domain instead
- Monitor and audit all activity (more on that later)

## When sh\*t hits the fan

So, as a backup, you should have at least two extra global admin accounts for emergency scenarios. Think about an MFA outage, and nobody can sign in, including all of your administrators. These accounts should be treated with care. When you create such accounts, make sure these accounts:

- are not synced accounts. They should be cloud-only
- are enabled at all time (think about your maintenance scripts that disable unused accounts)
- have the _@tenant.onmicrosoft.com_ domain as a suffix
- are excluded from all your conditional access policies
- do not have MFA enabled
- have passwords that not expire
- have permanent global admin roles. Don't use PIM.
- have a strong password, split up in two or three parts and stored separately in a save place (fireproof)
- are excluded from both Self Service Password Reset registration policy
- are excluded from Azure AD Identity Protection (registration) policy

Make sure you also have a routine or process in place in case you'll need these accounts in the future. Who is the product owner of Azure AD, and who are the approved admins that may use these credentials? Review and test this routine frequently.

![YubiKey 5 NFC](/assets/images/yubikey-5-nfc_3.png)

One thing that you could consider, is to use a FIDO2 security key for (one of) the emergency accounts. You don't need the username or password to sign-in, just the hardware key and a PIN. When an emergency is there, the admin never enters the credentials, so phishing and keylogging are not possible. It is possible to attach multiple keys to one account but with use of a different PIN.

```
Fellow blogger Daniel Chronlund already wrote some very good blogs on this topic, so I suggest you take a look at that too.

Break Glass Account Best Practices in Azure AD
An Azure AD Break Glass Routine Template for your Organization
Monitor your Azure AD Break Glass Accounts with Azure Monitor

```

## Monitoring & alerts

These break glass accounts should never be touched in any way. When the accounts are used, all the alarm bells should go off. But also think about scenarios when Conditional Access policies are created, and these accounts are not excluded. Or whats happens when these accounts are added to certain groups (think of dynamic groups) or disabled by maintenance scripts. You should have proper monitoring in place to be alerted when **_any_** activity is happening around these accounts.

The article of Daniel about Azure Monitor can help with that, but if you have Cloud App Security licenses, you can also create activity polices to be alerted when something happens to the accounts.

![](/assets/images/image-5.png)

![](/assets/images/police-fun-funny-uniform-33598-1024x681.jpg)

While writing this blog, I came up with another idea. Also, take a look at [this article](https://janbakker.tech/use-power-automate-as-your-it-policy-busted/) **_(Use Power Automate as your Conditional Access Police Department)_** This way, you can be sure that your break glass admin accounts are always excluded from Conditional Access policies.

## Let's wrap up

Emergency accounts are very important, but make sure that you check your routine frequently. Have extra monitoring in place for administrative events in general, but keep an extra eye on the break glass accounts. For more information, make sure you check the following resources:

[Protect your Microsoft 365 global administrator accounts | Microsoft Docs](https://docs.microsoft.com/en-us/office365/enterprise/protect-your-global-administrator-accounts#:~:text=Use%20strong%20passwords%20at%20least%2012%20characters%20long.&text=Store%20the%20passwords%20for%20the,dedicated%20global%20administrator%20user%20accounts.)

[Best Practices for Configuring the Global Admin Account in Office 365 | Alexander's Blog](https://www.zubairalexander.com/blog/best-practices-for-configuring-the-global-admin-account-in-office-365/)

[Manage emergency access admin accounts - Azure AD | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-emergency-access)

[Secure access practices for administrators in Azure AD | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

[Admin role descriptions and permissions - Azure AD | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

Stay safe!
