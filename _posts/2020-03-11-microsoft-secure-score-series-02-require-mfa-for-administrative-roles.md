---
title: "Microsoft Secure Score Series – 02 – Require MFA for administrative roles"
date: 2020-03-11
categories: 
  - "secure-score"
  - "security"
tags: 
  - "administrative-roles"
  - "azure-ad"
  - "mfa"
  - "pim"
  - "secure-score"
image: "/assests/images/meter-at-0000-1364700-scaled.jpg"
---

## Require MFA for administrative roles

_Requiring multi-factor authentication (MFA) for all administrative roles makes it harder for attackers to access accounts. Administrative roles have higher permissions than typical users. If any of those accounts are compromised, critical devices and data is open to attack._

* * *

In this post, we take a look at enabling MFA for your administrators. As stated in the description, users with administrative roles are interesting targets for hackers. Of course, it is recommended to enable MFA for **all** your users, but this post will focus on the privileged users only. I hope to cover the MFA rollout for users in another blogpost.

#### Impact

When you complete this action, your administrators will have to register for MFA. Depending on your policy, the affected users then get prompted for MFA when they login to cloud applications or do administrative tasks. This can have an impact on their day to day tasks. For example:

- Users with administrative roles will get prompted for MFA when accessing cloud resources such as email through the browser.
- Users with administrative roles will get prompted for MFA when using mobile or desktop applications.
- Users with administrative roles will get prompted when kicking off PowerShell scripts.
- Users with administrative roles will get promoted when enrolling a new device.

#### What about the license?

Enabling MFA for your admin users can be done in multiple ways. The most common way is to use a Conditional Access policy. This requires an Azure AD Premium **P1** license. In the next step, we are going to take a look at using dynamic groups, which also requires a P1 license. If you are using Azure AD Basic or Free, the options for MFA are limited. See [this article](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-mfa-licensing) for more information. If you have either an Azure AD Premium P2 or Enterprise Mobility + Security (EMS) E5 license, you can use Azure AD Privileged Identity Management (PIM) to enable MFA for your administrators. More on that later.

#### First things first

There a couple of things to keep in mind. When enabling this policy, all your selected roles will be impacted, including your service accounts for example. It is recommended to exclude your service accounts from this policy. The easiest way to this is by adding those accounts to an Azure AD security group and add them to the excluded groups in your policy. If you have a naming convention in place for your service accounts, you can use a [dynamic group](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/groups-dynamic-membership). New service accounts are then added to this group based on an attribute and will be excluded from your policy automatically. Using a dynamic group to exclude your service account is optional but is easier to maintain.

![](/assets/images/image-6.png)

Also, it's recommended to create emergency accounts, in case there is an MFA outage or a misconfiguration on your tenant that causes problems. This way, you can always log in, using a break glass account to change the configuration. You can exclude these accounts from your Conditional Access policy, just like your service accounts. [Learn how to create these specific accounts.](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-emergency-access)

Next, I would recommend enabling the [combined security information registration portal](https://docs.microsoft.com/en-us/azure/active-directory/authentication/concept-registration-mfa-sspr-combined) for your administrators. Using this portal, your administrators can register not only for MFA but also for Self Service Password Reset. Normally, this registration process would take place in two different portals. The new portal is in preview for quite a while now, but I like it much more than the "old" one. The wizard is more intuitive and smoother. You can enable this combined portal trough the **Azure portal -> Azure Active Directory -> User Settings -> User feature previews**

![](/assets/images/image-12.png)

![My Profile showing registered Security info for a user](/assets/images/combined-security-info-defualts-registered.png)

#### Implementation

Okay, so let's get started with creating the Conditional Access rule. Keep in mind, that you can test this on a small group first, by selecting just 1 single administrator role at step 3. Also, you can choose to enable the **_[report only](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/concept-conditional-access-report-only)_** mode at step 6, instead of turning the policy on. This way, you can first monitor the impact on a smaller group of users, or just monitor what would have happened when you enabled it right away.

![](/assets/images/msedge_Znjc0gtoQx-1008x1024.png)

In the [Azure portal Conditional Access page](https://go.microsoft.com/fwlink/?linkid=2095010)  
1\. Select + **New Policy**. Start off by giving it a proper name.  
2\. Go to Assignments > Users and groups > Include > **Select users and groups** > check **Directory roles**  
3\. At a minimum, select the following roles: (**_exclude your service - and break glass accounts_**)

- Security administrator
- Exchange service administrator
- Global administrator
- Conditional Access Administrator
- SharePoint administrator
- Helpdesk Administrator
- Billing Administrator
- User administrator
- Authentication Administrator

4\. Go to Cloud apps or actions > Cloud apps > Include > select **All cloud apps** (and don't exclude any apps)  
5\. Under Access controls > Grant > select **Grant Access** > check **Require multi-factor authentication** (and nothing else)  
6\. Enable policy > On  
7\. Create

#### (Admin)user experience

Administrators will get prompted to register for MFA the first they log in after the policy is enabled. Your users then can add multiple factors, depending on your MFA and SSPR settings.

![](/assets/images/image-8.png)

You can track these events using the Azure portal. Go to **Azure Active Directory -> Sign-ins** and filter for Status: **_Interrupted_**

![](/assets/images/image-10.png)

#### Azure AD Privileged Identity Management

If you want to take this a step further, and you have the right license, you can start using Azure AD Privileged Identity Management (PIM). Using this premium feature you can give your user just in time access (JIT) to privileged roles. This means that the privileged user is eligible to have certain admin roles, but does not have them permanently enabled. A quick overview of how Privileged Identity Management works:

1. Privileged Identity Management is set up so that users are **eligible** for privileged roles.
2. When an eligible user needs to use their privileged role, they activate the role in Privileged Identity Management.
3. Depending on the Privileged Identity Management settings configured for the role, the user must complete certain steps (such as performing **multi-factor authentication**, getting approval, or specifying a reason.)
4. Once the user successfully activates their role, they will get the role for a pre-configured time period.
5. Administrators can view a history of all Privileged Identity Management activities in the audit log. They can also further secure their Azure AD organizations and meet compliance using Privileged Identity Management features like access reviews and alerts.

This way, you can stay on top of your privileges users and use the [least privileged access](https://docs.microsoft.com/en-us/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) model.

When you consider rolling out PIM for your organization, read the [Microsoft docs](https://docs.microsoft.com/en-us/azure/active-directory/privileged-identity-management/pim-configure) on this subject. Apart from having MFA enabled for your admins, PIM can have a lot more value to your organization. It also can reduce costs and minimize risks.

![](/assets/images/image-11.png)

#### Wrap things up

You can enable MFA for your privileged users in multiple ways. You can either configure a conditional access policy, user Privileged Identity Management or enable MFA manually (not covered in this article). Try to automate a much as possible to avoid mistakes. Choose the option that fits your license and business requirements, but start **today** when you do not have this in place.

Stay safe!
