---
title: "Microsoft Secure Score Series – 07 – Turn on sign-in risk policy"
date: 2020-04-13
categories: 
  - "entra"
  - "secure-score"
  - "security"
tags: 
  - "azure"
  - "azure-ad-identity-protection"
  - "identity"
  - "mfa"
  - "policy"
  - "protection"
  - "secure-score"
  - "security"
  - "sign-in-risk"
coverImage: "man-in-black-holding-phone-618613-scaled.jpg"
---

## Turn on sign-in risk policy

_Turning on the sign-in risk policy ensures that suspicious sign-ins are challenged for multi-factor authentication._

* * *

Azure AD Identity Protection is powerful in many ways. As discussed in my [previous post](https://janbakker.tech/2020/03/27/microsoft-secure-score-series-04-ensure-all-users-can-complete-multi-factor-authentication-for-secure-access/) about Multi-factor Authentication, you can use IdP to onboard your user for MFA. Once you've had all your users registered, it's time to tighten your security by prompting for MFA when a sign-in is risky.

This improvement action wants you to turn on the sign-in risk policy in Azure Identity Protection. By enabling this policy, users will be prompted for MFA every time a suspicious login takes place. To give you a couple of examples, a suspicious sign-in is when:

- a user logs in from an anonymous IP address;
- a user logs in from a malware linked IP address;
- unfamiliar sign-in properties are detected;
- impossible travel happens;
- suspicious inbox manipulation rules are created.

The sign-in risk is calculated at the moment the user logs in and can increase the overall user risk. You can also enable a policy to remediate the overall user risk, but for now, we focus on the sign-in risk policy.

![Simplified risk structure..png](/assets/images/large)

#### First, let's talk money

As many great security features, this one requires an Azure AD Premium P2 license for every user that benefits from this feature. You can configure IdP from the Azure Portal.

![](/assets/images/image-19.png)

#### Configure the policy

To enable the policy, go to Azure Identity Protection and look for the user sign-in policy.

![](/assets/images/image-20.png)

There are 3 settings that needs to be configured:

1. **_Users_**. Which users do you want to protect? You can also make exclusions when needed.
2. _**Sign-in risk**._ At what sign-in risk level you want your policy to kick in? **Low and above**, **Medium and above** or **High**.
3. **_Access_**. What is the action to be taken? You can block access or prompt for MFA.

Last but no least, make sure the policy is **enabled**.

#### Conditonal Access

User sign-in risk is also a condition in Conditional Access. This way you can make more polices for specific user groups or scenarios. You can also combine conditions and make use of the named locations. To give you an idea of what you can do:

- Block access for **privileged** accounts when sign-in risk is high
- Block access from certain **locations** when sign-in risk is high
- Prompt for MFA when sign-in risk is medium or above, and the device is **unmanaged**.

![](/assets/images/image-21.png)

Another reason to use Conditional Access is the use of the Report-only feature. This way you can measure the impact first.

#### User & admin experience

To simulate a suspicious sign-in, I use a TOR browser to access Office 365. This simulates the use of an anonymous IP address, so the sign-in risk is medium. The user is prompted for MFA.

![](/assets/images/image-22.png)

In the Azure portal, you can verify the risky sign-in.

![](/assets/images/image-23.png)

#### Wrap things up

![](/assets/images/f8deef878867dee11b7a15a0a55765b4-1024x1024.jpg)

With the use of Azure AD Identity Protection, you can improve your security, and also your end-user experience. Your users are prompted for MFA or blocked, but only when needed. Using Conditional Access, you can make multiple polices for each scenario.

Stay safe!
