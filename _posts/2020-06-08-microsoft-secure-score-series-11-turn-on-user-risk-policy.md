---
title: "Microsoft Secure Score Series – 11 – Turn on user risk policy"
date: 2020-06-08
categories: 
  - "entra"
  - "secure-score"
  - "security"
tags: 
  - "azure-ad-identity-protection"
  - "block-access"
  - "password-reset"
  - "risk"
  - "risky"
  - "sign-in-risk"
image: "/assests/images/black-and-grey-keys-792034-scaled.jpg"
---

## Turn on user risk policy

_With the user risk policy turned on, Azure AD detects the probability that a user account has been compromised. As an administrator, you can configure a user risk conditional access policy to automatically respond to a specific user risk level. For example, you can block access to your resources or require a password change to get a user account back into a clean state._

Today, we take a look at Azure AD Identity Protection. More specifically, the ability to take automatic action on your risky users. Risky users can be blocked or forced to reset their passwords.

## What are risky users?

User risk is a calculation of the probability that an identity has been compromised. This is based on the "normal" behavior of the users. Identity Protection can detect leaked credentials and uses Azure AD threat intelligence to detect whether a user account is likely breached. Administrators can set up a policy so that users can self-remediate this risk. Risks are detected both in realtime and offline.

Azure Identity Protection [can detect](https://docs.microsoft.com/en-us/azure/active-directory/identity-protection/concept-identity-protection-risks) (among other things):

- **Anonymous IP address** (mostly TOR browsers and anonymous VPN)
- **Unfamiliar sign-in properties** (new device, location or behavior that is new to the given user)
- **Malware linked IP address** (malware-infected devices can be detected, as they send data to malicious servers)
- **Atypical travel** (sign-ins from locations that are different from other recent sign-ins)
- **Malicious IP address** (IP addresses that have bad reputations, mostly caused by a large amount of failed sign-ins)

Some of the detections are discovered by Microsoft Cloud App Security:

- **Suspicious inbox manipulation rules** (dubious inbox rules that are created to intentionally hide messages)
- **Impossible travel** (sign-ins are compared geographically, to detect breached accounts)

You can check for risky sign-ins and risky users in the [Azure portal.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/IdentityProtectionMenuBlade/RiskyUsers)

![](/assets/images/image-43.png)

## Licenses

Using this feature requires an Azure AD Premium P2 license. Self Service Password Reset is part of the Azure AD Premium P1 license.

**_TIP:_** you can easily activate a trial license. This gives you 100 Azure AD Premium P2 licenses for 30 days. During this trial, you can check out your risky users and see if any of those users have leaked credentials.

![](/assets/images/image-51.png)

## Automation

When you have Identity Protection running, you can go through the list of risky sign-ins yourself to invest and block users manually. With the use of the risk policy, you can automate this very easily. Configuring this policy takes only minutes and is very powerful.

Simply pick the user risk level and the controls to be enforced.

- ![](/assets/images/image-44.png)
    
- ![](/assets/images/msedge_e3PrP944IG-1024x520.png)
    

It is recommended to enable this for all your (licensed) users, but you should exclude your break glass accounts. Your policy would look something like this:

![](/assets/images/msedge_3YvUYRKfLJ.png)

## User experience - Block access

When you have configured your policy to **block access**, the user will get prompted with the following screen during sign-in:

![](/assets/images/firefox_SJ5BUtk6Um-1.png)

## User experience - Require password change

```
NOTE: Users that triggers the policy must have registered for password reset before. Otherwise, the access get's blocked. If you have not implemented SSPR, I suggest you read this and this blog first. 
```

![](/assets/images/firefox_arbiNCj1q6-1.png)

When users have registered before and triggered the policy, they get prompted for password change. The actual flow will depend on your SSPR configuration.

- ![](/assets/images/msedge_3twwu198I8-2.png)
    
- ![](/assets/images/msedge_65DY33SAyO-1.png)
    
- ![](/assets/images/msedge_ID5RpMcbWR-1.png)
    

## Test your policy

To test your policy, you can use a TOR browser. Sign-in a few times and refresh your identity each time. Because you are using a TOR browser, each sign-in will be risky. You can easily pick a new identity using this button:

![](/assets/images/image-50.png)

The result is a few sessions from different locations that will trigger risky sign-in policies.

![](/assets/images/image-48.png)

## Wrap up

Setting up a user risk policy will take the heat of your service desk and IT admins. Risky users can self-remediate without the help of someone else. With the use of Identity Protection, your users will have an extra layer of security. It is recommended to enable this feature for you privileged users like administrators, C(E)(O)(T)O's, and accounts that are an obvious target for spear phishing. Even better; enable for all your users when your budget allows it.

You can only configure **one** user risk policy per tenant. The **_sign-in risk_** policy, on the other hand, can be integrated with Conditional Access to have more granular control.

For more information, I suggest you take a look at [this page](https://docs.microsoft.com/en-us/azure/active-directory/identity-protection/overview-identity-protection). And don't forget to check out the other articles in the [Secure Score Series](https://janbakker.tech/category/secure-score/).

Stay safe!
