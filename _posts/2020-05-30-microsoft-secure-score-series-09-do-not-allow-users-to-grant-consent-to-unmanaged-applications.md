---
title: "Microsoft Secure Score Series – 09 – Do not allow users to grant consent to unmanaged applications"
date: 2020-05-30
categories: 
  - "entra"
  - "secure-score"
  - "security"
tags: 
  - "approval"
  - "apps"
  - "azure-ad"
  - "classifications"
  - "consent"
  - "deny"
  - "office-365"
  - "permissions"
image: "/assets/images/ethnic-woman-doing-stop-gesture-with-palm-at-camera-3880939-scaled.jpg"
---

## Do not allow users to grant consent to unmanaged applications

_Tighten the security of your services by regulating the access of third-party integrated apps. Only allow access to necessary apps that support robust security controls. Third-party applications are not created by Microsoft, so there is a possibility they could be used for malicious purposes like exfiltrating data from your tenancy. Attackers can maintain persistent access to your services through these integrated apps, without relying on compromised accounts._

* * *

Today we take a look at a serious problem in the modern IT landscape: consent to 3rd party applications. First, we need to understand what app consent is.

![](/assets/images/msedge_QuGwuHJmSi.png)

Let's say one of your users is going to join the Microsoft Tech Community website. When the users sign up, the application requests a couple of permissions. These permissions are used to access resources on behalf of the user. In this case, Microsoft Tech Community asks for the following permissions:

- Sign you in and read your profile
- Maintain access to data you have given access to

![](/assets/images/image-96.png)

You can review the given permissions for a user, using the Azure admin portal.

- ![](/assets/images/msedge_e0XnSfAgNH.png)
    
- ![](/assets/images/image-97.png)
    

This seems harmless at first, but imagine a 3rd party application that asks for way more permissions and your users can accept this. This permission will be staying there, even when the users' password changes. Even MFA won't stop this kind of access.

Greedy hackers have found their way into this consent framework, and this can [trick people](https://info.phishlabs.com/blog/office-365-phishing-uses-malicious-app-persist-password-reset) to give permissions that later on can be used to [steal information and data](https://www.bleepingcomputer.com/news/security/phishing-attack-hijacks-office-365-accounts-using-oauth-apps/). This attack is also known as an "[illicit consent grant attack](https://docs.microsoft.com/en-us/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants?view=o365-worldwide)".

```
When you suspect an attack, you can use Microsoft Cloud App Security to hunt for permissions and setup alert rules. 
```

#### How to fix it?

Now that we understand how this works, let's go on with prevention. You can prevent your users from granting access permissions on 3rd party apps. By using this setting, users will no longer be able to consent permissions to 3rd party apps. This can be done by using the **Azure Active Directory admin center -> Azure Active Directory -> Enterprise Applications -> User settings**

![](/assets/images/image-98.png)

The same setting reflects in your Office 365 admin center under **Settings ->Org settings -> User consent to apps**

![](/assets/images/image-99.png)

Users can no longer accept consent permissions to 3rd party apps. They get prompted with the following message:

![](/assets/images/image-100.png)

Administrators can approve, block or deny the requests by using the Azure admin center.

- ![](/assets/images/msedge_sqP2NX1qwh.png)
    
- ![](/assets/images/image-104.png)
    

#### What's next?

Now in preview, you can setup Admin consent requests. This way, the users can request consent to administrators. The users can enter a motivation on why they need access to the app. Additionally, you can configure to receive email notifications when admin consent requests are created.

The user experience will look like this: ​

- ![](/assets/images/image-101.png)
    
- ![](/assets/images/msedge_1kh5RSB3E1.png)
    

#### But wait! There's more!

Until now, this setting was either all or nothing. Now it's possible to configure [Consent and permissions | Permission classifications.](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ConsentPoliciesMenuBlade/UserSettings) This feature is currently in preview. This way you can label permissions to "low impact" and grant users consent this specific permissions, while all other permissions have to be granted by admins.

```
Users can only consent to apps that were published by a verified publisher and apps that are registered in your tenant. Users can only consent to the permissions you have classified as "Low impact"
```

- ![](/assets/images/msedge_FDx6Sw3hPo.png)
    
- ![](/assets/images/msedge_OIr2qaUxIw.png)
    

#### Let's wrap up

If you want the most control and best security, disabling the user consent would be your best option. But this option backfires on your administrators because they have to approve each consent. When adding the verified publishers and low impact permissions, you can take away some (or most) of that work. This will depend mostly on whether your apps have verified publishers, and what you define as low impact permissions.

![](/assets/images/App-consent-3.png)

Kenneth van Surksum already did [a thorough article](https://www.vansurksum.com/2020/05/22/some-welcome-additions-to-the-admin-consent-workflow-in-azure-ad/) on the new additions for admin consent a few weeks ago. I suggest you check that one out too. Next to that, please take a look at the Microsoft documentation:

- [Configure how end-users consent to applications](https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/configure-user-consent)
- [Publisher verification and app consent policies are now generally available - Microsoft Tech Community](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/publisher-verification-and-app-consent-policies-are-now/ba-p/1257374)
- [Configure the admin consent workflow (preview)](https://docs.microsoft.com/en-US/azure/active-directory/manage-apps/configure-admin-consent-workflow)

Stay safe!
