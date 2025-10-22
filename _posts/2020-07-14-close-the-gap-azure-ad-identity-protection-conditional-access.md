---
title: "Close the gap. Azure AD Identity Protection & Conditional Access."
date: 2020-07-14
categories: 
  - "entra"
  - "security"
tags: 
  - "automation"
  - "azure-ad-identity-protection"
  - "conditional-access"
  - "risk"
  - "risk-detections"
  - "risky-users"
  - "sign-in-risk"
image: "/assets/images/cyclone-fence-in-shallow-photography-951408-2.jpg"
---

This blog is about Azure AD Identity Protection and Conditional Access, and how these two features are working together. This is not my first article on this subject. (neither my last) In previous blogs, I covered the [sign-in risk](https://janbakker.tech/microsoft-secure-score-series-07-turn-on-sign-in-risk-policy/) and [user risk](https://janbakker.tech/microsoft-secure-score-series-11-turn-on-user-risk-policy/) policies as part of the Secure Score Series, and in my blog, about [Azure Multifactor Authentication](https://janbakker.tech/microsoft-secure-score-series-04-ensure-all-users-can-complete-multi-factor-authentication-for-secure-access/) I talked about Azure AD Identity Protection, and how it can be used to roll out MFA in your organization.

Microsoft released the [user risk integration](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/whats-new#user-risk-condition-in-conditional-access-policy) with Conditional Access last week, so for me, it seemed the perfect occasion to write a blog about this. To understand why this integration is such a big deal, let's first take a look at those two features individually.

## What is Conditional Access?

Conditional Access is a feature in Azure Active Directory and requires a **Premium P1** license. It can be used to protect your Office 365 and Azure AD resources. I often call it: " the firewall of the cloud". You can deploy _if-this-than-that_ statements to determine who has access to resources and under what conditions.

![Conceptual Conditional signal plus decision to get enforcement](/assets/images/conditional-access-signal-decision-enforcement.png)

There are a lot of [signals](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/overview#common-signals) and conditions that can be used. Also, Conditional Access integrates with many other security features such as Microsoft Defender ATP, Intune (App Protection), and Cloud App Security. You can build policies like:

- To access Exchange Online from an unmanaged device, all users have to perform MFA.
- Users in the sales team can not access the CRM application unless they are using a managed device.
- When users access SharePoint Online from an untrusted device, they cannot download or upload documents.
- Users who access Onedrive for Business on their iOS or Android device must use the (protected) Onedrive app.

![Conceptual Conditional Access process flow](/assets/images/conditional-access-overview-how-it-works.png)

## What is Azure AD Identity Protection?

Azure AD Identity Protection is also a premium feature in Azure Active Directory but requires a **_Premium P2_** license. The feature is all about risk detection and remediation. Identity protection uses Azure AD threat intelligence to determine whether the sign-ins are [risky](https://docs.microsoft.com/en-us/azure/active-directory/identity-protection/concept-identity-protection-risks). In case of a risky sign-in, the user can self-remediate by approving the MFA request.

All the sign-ins are aggregated so that the **_user risk_** is calculated. This happens both in real-time and offline. You can investigate the risks from the Azure portal, or export the risk data to analyze it elsewhere.

![](/assets/images/image-16.png)

Multiple risks can be detected. The most important one is the ability to detect **_leaked credentials_**. To take advantage of that feature, you'll have to turn on password hash synchronization. You can read all about that part in [one of my previous blogs.](https://janbakker.tech/microsoft-secure-score-series-03-enable-password-hash-sync-if-hybrid/) There are a couple of other risks that can be identified:

- Atypical travel
- Anonymous IP address
- Unfamiliar sign-in properties
- Malware linked IP address
- Known attack patterns

Malicious IP addresses are detected when attackers use dictionary attacks, brute force, password spray, or list cleaning methods.

When you are licensed for **_Cloud App Security_**, Identity Protection can also detect additional risks such as suspicious inbox manipulation rules and impossible travel.

#### Risk and remediation

So, Azure AD Identity Protection gives you two parameters that you can work with:

- **_User risk_** (represents the probability that a given identity or account is compromised.)
- **_Sign-in risk_** (represents the probability that a given authentication request isn't authorized by the identity owner.)

_Take a look at this overview to understand the flow of Azure AD Identity Protection._

![](/assets/images/Identity-Protection-Flow-2.png)

For both risk types, you can set up policies to let users remediate the risk. Users with a risky sign-in can self-remediate by satisfying the MFA challenge. Users that trigger the user risk policy, can be blocked or forced to reset their password.

![](/assets/images/image-18-1024x508.png)

That brings us to the core of this blog post. As these policies apply for **all** of the Azure resources and applications, some organizations want to configure some granularity on top, or instead of these policies.

## Conditional Access goodness

As of today, both **_user and sign-in risk_** can be used as conditions in Conditional Access. The 'sign-in risk' condition is out of preview already, and Microsoft recently added the 'user risk' condition. Take note this feature is still in preview at the time of writing.

![](/assets/images/image-19-1024x992.png)

Next, you can configure the action. You can block access, or force a password reset. When you pick "Require Password change (Preview)", take note of these three things:

- **_"Require multi-factor authentication"_** is also checked
- This will only work when you select **_"All cloud apps"_**
- You can only select the **_User risk (Preview)_** condition in combination with password reset. All other conditions are greyed out.

- ![](/assets/images/252-14-07-2020.png)
    
- ![](/assets/images/251-14-07-2020.png)
    

User risk support in Azure AD Conditional Access policy allows you to create multiple user risk-based policies. Different minimum user risk levels can be required for different users and apps. Based on user risk, you can create policies to block access, require multi-factor authentication, secure password change, or redirect to Microsoft Cloud App Security to enforce session policy, such as additional auditing. Also, you could make use of the **_report-only_** setting to measure user impact first.

```
Microsoft's recommendation is to require password change for users on high risk.
```

## Sign-in experience

For this demo, I created a policy that will force a password reset once the user's risk level is **medium** or **high**. Once the user has reached that point, the user will be prompted for MFA first. After successful authentication, the user can change the password.

- ![](/assets/images/245-14-07-2020-892x1024.png)
    
- ![](/assets/images/246-14-07-2020-838x1024.png)
    

#### Good to know

When you have configured either **_"Require multi-factor authentication"_** or **"Require password change (Preview)"** and the user has not registered for MFA, the session will be blocked.

![](/assets/images/image-23.png)

![](/assets/images/244-14-07-2020.png)

## So, why is this better?

As I mentioned earlier, organizations can now deploy more fine-grained and specific policies, based on user risk. You can now use multiple conditions and make full use of the exclusions. You could build a policy to block access for high-risk administrator accounts, and have another one for your regular users with different conditions and controls.

Azure AD Identity Protection is a **_must-have_** if you ask me. At least for your administrators and users that have access to intellectual property or high confidential data. When those users get breached, it will most likely damage your companies reputation and cost a lot of money and effort to restore the damage. That's an easy business case right? You can try Azure Premium for free. This will give you instant insight into your risky users.

![](/assets/images/image-24.png)

## Resources

I suggest you take a look at this video: [Ignite 2019 session about Azure Active Directory Identity Protection](https://myignite.techcommunity.microsoft.com/sessions/81723?source=sessions)

![](/assets/images/image-20.png)

This will give you some in depth information about how risk is detected and how you can integrate this product with other systems.

Also check out the Microsoft documentation to learn more about the premium features:

[Azure AD Conditional Access documentation](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/)

[Azure AD Identity Protection documentation](https://docs.microsoft.com/en-us/azure/active-directory/identity-protection/)

Also, check out these [base protection policies](https://github.com/AlexFilipin/ConditionalAccess/tree/master/PolicyRepository/Base%20protection) to get you started.

**_Update 02-2021:  
_**Sometimes I get the question if you can send out alerts to end-users so that they can respond to sign-ins in order to train the system. However, check out this note that I found in the [FAQ](https://docs.microsoft.com/en-us/azure/active-directory/identity-protection/troubleshooting-identity-protection-faq):

_Today, selecting confirm safe on a sign-in does not by itself prevent future sign-ins with the same properties from being flagged as risky. The best way to train the system to learn a user's properties is to use the risky sign-in policy with MFA. When a risky sign-ins is prompted for MFA and the user successfully responds to the request, the sign-in can succeed and help to train the system on the legitimate user's behavior._

_If you believe the user is not compromised, use **Dismiss user risk** on the user level instead of using **Confirmed safe** on the sign-in level. A **Dismiss user risk** on the user level closes the user risk and all past risky sign-ins and risk detections._

Stay safe!
