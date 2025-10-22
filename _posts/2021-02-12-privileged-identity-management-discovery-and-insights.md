---
title: "Privileged Identity Management Discovery and insights"
date: 2021-02-12
categories: 
  - "entra"
  - "security"
image: "/assets/images/image-13.png"
---

Privileged Identity Management (PIM) in Azure Active Directory is getting more and more popular. But how do you get started? Like any successful project, it all starts with a good inventory of the current situation. You need to identify the problem before it can be resolved. The problem we are talking about is standing access to high privilege roles. If you are not familiar with PIM, please check out [this blog post](https://janbakker.tech/active-directory-identity-governance-privileged-identity-management/) first.

[Discovery and insights](https://docs.microsoft.com/en-us/azure/active-directory/privileged-identity-management/pim-security-wizard), formerly known as Security Wizard, is here to help you. It's a great new feature in PIM that can help you to identify who has standing access to high privileged roles. In short, it helps you to do these three things:

1. Reduce Global Administrators
2. Convert standing access (active roles) to eligible for high privileged roles
3. Review service principals with active roles

![](/assets/images/1612982400.png)

## 1\. Reduce Global Administrators

**Microsoft recommends** that you keep two break glass accounts that are permanently assigned to the global administrator role. Make sure that these accounts don't require the same multi-factor authentication mechanism as your normal administrative accounts to sign in, as described in [Manage emergency access accounts in Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/roles/security-emergency-access).

Next to that, you want to make sure that you keep the number of users with the global administrator role to a minimum. Global administrators are able to control all aspects of your Azure AD tenant and can touch every setting in Microsoft 365 for example. If one of these accounts is breached, the attacker has full control. Reducing the number of global administrators will reduce the attack surface instantly.

Go to [Global Administrator - Microsoft Azure](https://portal.azure.com/#blade/Microsoft_Azure_PIMCommon/ResourceMenuBlade/aaddiscovery/resourceId//resourceType/tenant/provider/aadroles/defaultId/roles) and select **_Reduce global administrators_** to get started.

![](/assets/images/image-8.png)

Here you will find an overview of all users that are holding the Global admin role. Although this is just a demo environment, and I added these roles myself, this is often the view in many organizations. I've seen tenants with more than 60 Global Admins. That's like running a ship with 60 captains!

![](/assets/images/image-7.png)

Next, select the users and the action that you want to apply. You can either remove the assignment or make the role eligible for specific users. The user will be informed about the action via email.

![](/assets/images/image-9.png)

You can also create an access review where the users need to respond to keep their current role. Keep in mind that this will create an access review for ALL current global admins. Learn more about access reviews in my [previous post.](https://janbakker.tech/active-directory-identity-governance-access-reviews/)

![](/assets/images/image-10.png)

This is a self-review, and users are able to approve or deny the request. Administrators will receive an email to start the review.

![](/assets/images/image-11.png)

The review has recommendations and asks for justification. It will run for **_30_** days. PIM and global admins can then continue to check the outcome of the review and apply the changes.

![](/assets/images/image-12.png)

You can keep track of this access review under the Access Review section.

![](/assets/images/image-13.png)

## 2\. Eliminate standing access

The next wizard will give you the possibility to convert standing access to the most Highly Privileged Roles, like Exchange and SharePoint administrators. This will work the same as the previous example. You can remove the access completely, convert the assignment to eligible, or create an access review for self-review.

![](/assets/images/image-14.png)

The highest roles in Azure AD are (according to the wizard):

- Privileged Role Administrator
- User Administrator
- Security Administrator
- Application Administrator
- Exchange Administrator
- SharePoint Administrator
- Cloud Application Administrator
- Intune Administrator

## 3\. Service Principals

Last but not least, the wizard allows you to review the service principals with admin role assignments. Since service principals can only have active assignments (not eligible), these are a big risk and needs to be reviewed frequently.

With the use of the wizard you are able to review and remove the assignment for the service principal.

![](/assets/images/image-15.png)

## Wrap things up

This feature is still in preview, but it will speed up your PIM project guaranteed. The GUI is still a little bit buggy; you'll need to re-run the wizard sometimes to get rid of all the assignments, but overall this is a great feature.

Since identity is the new perimeter, security and governance around these subjects are very important. To get you started, please check out these three articles about Identity Governance:

[https://janbakker.tech/active-directory-identity-governance-access-reviews/](https://janbakker.tech/active-directory-identity-governance-access-reviews/)  
[https://janbakker.tech/azure-active-directory-identity-governance-azure-ad-entitlement-management/  
](https://janbakker.tech/azure-active-directory-identity-governance-azure-ad-entitlement-management/)[https://janbakker.tech/active-directory-identity-governance-privileged-identity-management/](https://janbakker.tech/active-directory-identity-governance-privileged-identity-management/)

Also, check out this [series about Secure Score](https://janbakker.tech/category/secure-score/), which will help you to get started with security in Microsoft 365.

Stay safe!
