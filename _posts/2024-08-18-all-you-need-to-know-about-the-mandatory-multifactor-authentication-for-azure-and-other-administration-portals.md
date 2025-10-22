---
title: "All you need to know about the mandatory multifactor authentication for Azure and other administration portals"
date: 2024-08-18
categories: 
  - "entra"
  - "security"
image: "/assets/images/pexels-olly-3771074-scaled.jpg"
---

In case you didn't get the latest memo, Microsoft is tightening the security around the Azure and Microsoft 365 admin portals by enforcing multifactor authentication for all interactive sign-ins. In this post, I will try to answer all the questions about this change, as there seem to be a lot of questions on social media lately. If you still have questions after reading this post (which I'm sure you have), please reach out so I can add your Q&A to the post. Here we go.

## What is happening?

Microsoft will enforce multifactor authentication (MFA) for "all Azure sign-in attempts." More specifically, this means MFA will be enforced for all management tools used to manage Azure resources, Microsoft 365, and Graph API.

Here's the entire list with resources that are impacted:

[Azure portal](https://portal.azure.com)  
[Microsoft Entra admin center](https://entra.microsoft.com)  
[Microsoft Intune admin center  
](https://manage.microsoft.com)[Azure command-line interface (Azure CLI)  
](https://learn.microsoft.com/en-us/cli/azure/)[Azure PowerShell  
](https://learn.microsoft.com/en-us/powershell/azure/)[Azure mobile app](https://learn.microsoft.com/en-us/azure/azure-portal/mobile-app/overview) [Infrastructure as Code (IaC) tools](https://learn.microsoft.com/en-us/devops/deliver/what-is-infrastructure-as-code)

## For who?

That's easy: all users who sign in to management tools like the Azure portal, Entra Admin Center, or Powershell. So that includes your administrators, break-glass emergency access accounts, and user accounts that are (mis)used as service accounts to run automated tasks or scripts.

Non-human identities (workload identities) are not impacted, as these accounts are not used to sign in interactively.

In the end, this change will land on all (more than 1 million) Microsoft tenants out there, whether it's production, test, development, hackbox, or playground.

## When?

This massive operation will be conducted in two waves. The first wave starts on October 15, 2024, when MFA will be enforced for the Entra Admin Center, Azure portal, and Intune Admin Center for all users.

The second wave will close the gap with resources such as Azure CLI, Azure PowerShell, Azure mobile app, and IaC tools in early 2025.

![](/assets/images/2024-08-16-000292.png)

Please refer to the [official documentation](https://learn.microsoft.com/entra/identity/authentication/concept-mandatory-multifactor-authentication#applications) for the latest updates.

## Why?

One of Microsoft’s [Secure Future Initiative](https://www.microsoft.com/en/microsoft-cloud/resources/secure-future-initiative) (SFI) key actions is ensuring Azure accounts are protected with securely managed, phishing-resistant multifactor authentication. Simply put, there are still too many unprotected accounts there, and hackers love them. MFA would stop roughly 90% of the attacks or slow the bad guys down. Personally, I would recommend not only enforcing MFA but also making sure authentication methods are phishing-resistant. It is too easy to phish for MFA credentials these days. Don't believe me? Check this: [How to set up Evilginx to phish Office 365 credentials - JanBakker.tech](https://janbakker.tech/how-to-set-up-evilginx-to-phish-office-365-credentials/)

## How do we know?

Microsoft will [notify all Global administrators](https://learn.microsoft.com/en-us/entra/identity/authentication/concept-mandatory-multifactor-authentication#notification-channels) through various channels. Still, I think it won't hurt to also forward this post to your IT manager and co-admins, as the coverage might not be enough to get the attention of your (busy) local IT heroes with Global admin roles.

The first channels are currently rolling out.

**Portal notification:** A notification appears in the Azure portal, Microsoft Entra admin center, and Microsoft Intune admin center when they sign in. The portal notification links to this topic for more information about the mandatory MFA enforcement.

![](/assets/images/2024-08-16-000291.png)

**Email**: Global Administrators who have configured an email address will be informed by email of the upcoming MFA enforcement and the actions required to be prepared.

![](/assets/images/2024-08-18-000301.png)

**Microsoft 365 message center**: A message appears in the Microsoft 365 message center with the same information as the email and service health notification. [MC862873 - Take action: Enable multifactor authentication for your tenant before October 15, 2024 | Microsoft 365 Message Center Archive (merill.net)](https://mc.merill.net/message/MC862873)

![](/assets/images/2024-08-18-000302.png)

## Act now! Enable MFA!

There is no need to wait until D-Day. Administrators with Entra ID P1 or P2 licenses can easily enable multifactor authentication using Conditional Access. Microsoft made it simple by providing a template that can be enabled in a few clicks.

![](/assets/images/2024-08-18-000303.png)

I strongly suggest protecting Azure management with MFA and enforcing strong, phishing-resistant authentication for all admins.  
  
[Require MFA for Azure management with Conditional Access - Microsoft Entra ID | Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/conditional-access/howto-conditional-access-policy-azure-management)  
[Require phishing-resistant multifactor authentication for Microsoft Entra administrator roles - Microsoft Entra ID | Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/conditional-access/how-to-policy-phish-resistant-admin-mfa)

Organizations with Entra ID Free can easily enable [Security Defaults](https://learn.microsoft.com/en-us/entra/fundamentals/security-defaults) to protect all users in their tenant.

## Measure impact

If you don't know what users are using the Azure manage tools today, you can easily build a report using the Microsoft Identity Tools Powershell module. [Export-MsIdAzureMfaReport | MSIdentityTools (azuread.github.io)](https://azuread.github.io/MSIdentityTools/commands/Export-MsIdAzureMfaReport/)

![](/assets/images/image.png)

## Security information registration process

If your privileged users are not registered for MFA yet, please also be aware that security information registration can be done on every device and from every location. That means that if an account is already breached, attackers can easily register for MFA themselves to ensure persistence.

Some organizations might have used trusted network location or device compliance to secure the registration experience. With the addition of [Temporary Access Pass](https://learn.microsoft.com/en-us/entra/identity/authentication/howto-authentication-temporary-access-pass) in Microsoft Entra ID, administrators can provide time-limited credentials to their users that allow them to register from any device or location. Temporary Access Pass credentials satisfy Conditional Access requirements for multifactor authentication. [Azure Active Directory Temporary Access Pass - JanBakker.tech](https://janbakker.tech/azure-active-directory-temporary-access-pass/)  
  
[Control security information registration with Conditional Access - Microsoft Entra ID | Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/conditional-access/howto-conditional-access-policy-registration)

## I use external MFA; now what?

Entra now supports external MFA providers (preview) and that does also satisfy the MFA requirement. If you still use ADFS (please send me a DM explaining why), make sure you [send the MFA claim](https://learn.microsoft.com/en-us/entra/identity/authentication/howto-mfa-adfs#secure-microsoft-entra-resources-using-ad-fs) to Entra ID.  
  
[How to manage an external authentication method (EAM) in Microsoft Entra ID (Preview) - Microsoft Entra ID | Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/authentication/how-to-authentication-external-method-manage)

## I'm sure there is an opt-out. Tell me where to find it!

Unfortunately, this change will be enforced on all tenants and for all users. There is currently no opt-out (which is good). However, for complex environments, Microsoft has something to delay the rollout. [Learn more](https://learn.microsoft.com/en-us/entra/identity/authentication/concept-mandatory-multifactor-authentication#request-more-time-to-prepare-for-enforcement)

Please consider this very carefully before going this route, as your resources will be unprotected for another few months. I strongly recommend enforcing MFA for all users _today_.

![](/assets/images/2024-08-16-000300.png)

## What about my emergency access / break-glass accounts

With this change, emergency accounts will also be impacted. Excluding them from all Conditional Access policies is still recommended, but as the account is used to sign into the Azure portal, for example, MFA will be enforced. Therefore, these accounts should also be enabled with MFA.

![](/assets/images/2024-08-18-000304.png)

My advice would be to enroll your accounts with a FIDO 2 security key. You can also consider adding more than one key to one account. Optionally, you can also enroll a software OTP token like a password manager in case someone messed up the FIDO configuration on the tenant by blocking specific AAGUIDS, for example. Testing your emergency access accounts is more important than ever.

Here's a good post from Merill that has guidance around securing emergency access accounts:

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">There are more details on this new docs page.<br><br>📗 Planning for mandatory multifactor authentication for Azure and other administration portals <a href="https://t.co/FwFgkAvLRM">https://t.co/FwFgkAvLRM</a><br><br>Emergency access accounts are not excluded from this, so remember to set up MFA against them.<br><br>Please let all… <a href="https://t.co/JrfBxkTgOR">pic.twitter.com/JrfBxkTgOR</a></p>— Merill Fernando (@merill) <a href="https://twitter.com/merill/status/1824284669028048988?ref_src=twsrc%5Etfw">August 16, 2024</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

Here, you can find a step-by-step tutorial to enroll in a break-glass account with a FIDO2 security key. [Break glass accounts and Azure AD Security Defaults - JanBakker.tech](https://janbakker.tech/break-glass-accounts-and-azure-ad-security-defaults/)

## How can I keep track in the logs?

The Authentication Details tab in the details of a sign-in log provides the following information for each authentication attempt:

- A list of authentication policies applied, such as Conditional Access or Security Defaults.

- The sequence of authentication methods used to sign-in.

- If the authentication attempt was successful and the reason why.

[Learn about the sign-in log activity details - Microsoft Entra ID | Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/monitoring-health/concept-sign-in-log-activity-details#authentication-details)

![](/assets/images/image-1.png)

## Wrap up

Some might think: is this really needed? We all have MFA enabled by now, right? Well. If that were the case, the world would be a better place. I see a lot of customer tenants, and I'm really concerned about the gaps and exclusions in the MFA enforcement.

Please stay tuned for more Q&A in the coming months. Stay safe!

Learn more:

[Mandatory Microsoft Entra multifactor authentication (MFA) - Microsoft Entra ID | Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/authentication/concept-mandatory-multifactor-authentication#request-more-time-to-prepare-for-enforcement)  
[Announcing mandatory multi-factor authentication for Azure sign-in | Microsoft Azure Blog](https://azure.microsoft.com/en-us/blog/announcing-mandatory-multi-factor-authentication-for-azure-sign-in/)  
[Update on MFA requirements for Azure sign-in - Microsoft Community Hub](https://techcommunity.microsoft.com/t5/core-infrastructure-and-security/update-on-mfa-requirements-for-azure-sign-in/ba-p/4177584)
