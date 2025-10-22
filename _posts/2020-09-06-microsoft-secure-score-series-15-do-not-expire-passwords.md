---
title: "Microsoft Secure Score Series – 15 – Do not expire passwords"
date: 2020-09-06
categories: 
  - "entra"
  - "secure-score"
  - "security"
tags: 
  - "azure-ad"
  - "azure-ad-identity-protection"
  - "password-reset"
  - "passwords"
  - "secure-score"
  - "security"
coverImage: "pexels-andrea-piacquadio-3755755-scaled.jpg"
---

## Do not expire passwords

```
Research has found that when periodic password resets are enforced, passwords become less secure. Users tend to pick a weaker password and vary it slightly for each reset. If a user creates a strong password (long, complex and without any pragmatic words present) it should remain just as strong in 60 days as it is today. It is Microsoft's official security position to not expire passwords periodically without a specific reason, and recommends that cloud-only tenants set the password policy to never expire.
```

Passwords are evil. They haunt us for decades now, and it's hard to get rid om them. To put this recommendation in perspective, we have to understand what the problem with passwords is, and why its better not to change them frequently.

## The problem with passwords

An average person has 90+ accounts connected to their email address. All these services require strong passwords, so users have to come up with 90+ different passwords and after that, users have to remember them. Instead of that, most users pick one or two passwords and use them for all those services. Passwords from personal services are used for work, and visa versa.

![](/assets/images/pexels-andrea-piacquadio-3755755-1024x683.jpg)

## Protect, don't change

In earlier blogs, I wrote about [Microsoft Identity Protection](https://janbakker.tech/tag/azure-ad-identity-protection/). This premium feature can protect our identity in Azure Active Directory. Leaked passwords can be detected (need [password hash sync](https://janbakker.tech/microsoft-secure-score-series-03-enable-password-hash-sync-if-hybrid/)), and anomalies can be detected and automatically remediated. When passwords are leaked by either phishing, breach, or password attack, the user's risk is raised and password change can be enforced. This is a valid reason to change a passwords.

Enforcing users to change their password on a regular base is not more secure. In most cases, the password is just slightly changed. In these cases, the next password can be predicted based on the previous password. Password expiration requirements offer no containment benefits because cyber criminals almost always use credentials as soon as they compromise them. For example, _'SecureP@ssword1'_ becomes _'SecureP@ssword2'_. Next to that, users are more likely to forget their brand new passwords, resulting in another password reset.

![](/assets/images/4c4r3m.jpg)

The same applies to long and complex passwords. If you require longer passwords, the user will end up with passwords like '_Passw0rdPassw0rd_' and if you require a complex password, the users will have certain patterns in their password. For example, a capital letter in the first position, a symbol in the last, and a number in the last 2. Hackers know this stuff more than anyone in the world, so they use this knowledge for password attacks.

## Multi-Factor & Legacy Authentication

This seems like an open door, but when you enable MFA for your users, and the password is stolen somehow, the attacker is stopped when they try to use these credentials. Also, keep in mind to [disable legacy authentication](https://janbakker.tech/microsoft-secure-score-series-06-enable-policy-to-block-legacy-authentication/), because attackers will rather take '_the backdoo_r' where no MFA can be enforced. (POP, SMTP, IMAP, and MAPI)

From the Microsoft docs:

The numbers on legacy authentication from an analysis of Azure Active Directory (Azure AD) traffic are stark:

- More than 99 percent of password spray attacks use legacy authentication protocols
- More than 97 percent of credential stuffing attacks use legacy authentication
- Azure AD accounts in organizations that have disabled legacy authentication experience 67 percent fewer compromises than those where legacy authentication is enabled

## Edit the password policy

In the [Microsoft 365 admin center](https://go.microsoft.com/fwlink/?linkid=2095515) go to Settings > Security & privacy. Then **Edit** the password policy to never let passwords expire. You must be a global admin to edit the password policy.

![](/assets/images/image-9.png)

## Wrap things up

To make sure that your users pick secure passwords, you can configure [Azure AD Password Protection](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-password-ban-bad-on-premises), which you can also extend to on-premises. Password protection detects and blocks known weak passwords and their variants.

As I stated in the intro, passwords are evil. We have to get rid of them as soon as possible. Please have a look at FIDO2 solutions, to embrace [passwordless authentication](https://www.microsoft.com/en-us/security/business/identity/passwordless). You can start today!

More information about password recommendations:

[https://docs.microsoft.com/en-us/microsoft-365/admin/misc/password-policy-recommendations?view=o365-worldwide](https://docs.microsoft.com/en-us/microsoft-365/admin/misc/password-policy-recommendations?view=o365-worldwide)  
[https://www.ftc.gov/news-events/blogs/techftc/2016/03/time-rethink-mandatory-password-changes](https://www.ftc.gov/news-events/blogs/techftc/2016/03/time-rethink-mandatory-password-changes)

Stay safe!
