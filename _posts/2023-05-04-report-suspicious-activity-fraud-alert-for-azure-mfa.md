---
title: "Report Suspicious Activity & Fraud Alert for Azure MFA"
date: 2023-05-04
categories: 
  - "entra"
  - "security"
coverImage: "pexels-dmitriy-ganin-7537821-scaled.jpg"
---

A new feature popped up in Azure AD. Well, not entirely new, I must say. Reading from the [docs](https://learn.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-mfasettings#report-suspicious-activity), Report Suspicious Activity is an enhancement of the Fraud Alert feature that has existed for quite some time.

Until now, administrators could enable _Fraud Alert_ for Azure MFA so that users could report when suspicious MFA prompts are received. Users who reported fraud could be automatically blocked so they could no longer sign in. As this is a good feature, it kills productivity, as the intervention of an admin can only remediate the user. This admin would then need to unblock the user and ensure the user's identity was secured.

![](/assets/images/image.png)

Well, good news for customers who own an Azure AD Premium P2 license: you can now integrate Fraud Alert with Azure AD Identity Protection. You can also configure the reporting code for users who use voice calls as an authentication method.

![](/assets/images/My-new-creation-1.png)

From the Azure AD portal, go to Security -> Authentication Methods -> Settings. Here you will find a new policy that can be enabled for a single group or all users.

![](/assets/images/image-1.png)

Once enabled, users can now report suspicious activity from either Authenticator App or voice call. For the purpose of this demo, I use the Authenticator app.

![](/assets/images/IMG_1416-2.png)

![](/assets/images/IMG_1417-2.png)

Fraud Alert and the new Report Suspicious Activity can be used together. Keep in mind: if Fraud Alert is enabled with Automatic Blocking, and Report Suspicious Activity is enabled, the user will be added to the blocklist and set as high-risk and in-scope for any other policies configured. These users will need to be removed from the blocklist and have their risk remediated to enable them to sign in with MFA.

After the user reports fraud, the user risk is set to high.

![](/assets/images/msedge_eETqYag12j.png)

The audit logs will also report that a user reports suspicious activity.

![](/assets/images/msedge_keUpNBKzOa.png)

With this in place, you can now integrate with Conditional Access to force a password reset for users with high user risks. For some personas, you can also consider blocking the user.

![](/assets/images/image-2.png)

![](/assets/images/image-3.png)

With this policy in place, Adele is now forced to reset her password and self-remediate the risk.

![](/assets/images/image-4.png)

As you can see, Adele could do self-remediation and stay productive and secure!

![](/assets/images/image-5.png)

The Fraud Alert feature is available for both P1 and P2, while the new Report Suspicious Activity feature is only available in P2, as this integrates with Azure AD Identity Protection.

![](/assets/images/image-6.png)

More info can be found here: [Configure Azure AD Multi-Factor Authentication - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-mfasettings#report-suspicious-activity)

Stay safe!
