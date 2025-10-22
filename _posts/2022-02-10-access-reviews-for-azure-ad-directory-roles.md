---
title: "Access reviews for Azure AD directory roles"
date: 2022-02-10
categories: 
  - "entra"
  - "security"
image: "/assests/images/pexels-pixabay-416322.jpg"
---

This blog post is for all those organizations out there with stale, overprivileged accounts having standing access to Azure AD roles, that nobody knows about, far away from the HR systems and on/offboarding processes. This is often a huge problem and the elephant in the (security) room. Now, what can we do about it?

I assume you are already aware of Azure AD Privileged Identity Management, and the great features that it brings. In short: with PIM you can reduce the risk for your AD roles (and resources) by delivering just in time and just enough privileges to your users. You can eliminate standing access and require MFA, approval and/or justification before admin roles can be activated or resources can be managed. If you're new to Privileged Identity Management, please read [this post](https://janbakker.tech/privileged-identity-management-discovery-and-insights/) first.

Now, what we just discussed is just the technical part. Let's talk about processes because giving out access is one thing, but one very important thing is often forgotten: what if the role is no longer needed? Who is taking care of that?

## Access Reviews

Enter: Access Reviews. This feature is part of Privileged Identity Management and Identity Governance (both Premium P2) and really helps you to automate this cumbersome process. And the keyword here is **_delegation_**.

Let's see how it works. Head over to the Azure Portal and go to the Privileged Identity Management console. Select **_Azure AD roles_** from the menu. Under the **_Manage_** section, select **_Access reviews_** and click **_New_** to create our first access review for Azure AD directory roles. [Smartlink](https://portal.azure.com/#blade/Microsoft_Azure_PIMCommon/ResourceMenuBlade/AccessReviews/resourceId//resourceType/tenant/provider/aadroles).

![](/assets/images/image.png)

For this demo, we are going to create an access review for Exchange administrators.

1. Give your review a proper name and description.
2. Pick the date for your review.
3. Select the frequency. For now, we select **_On time_**, but you can make this a recurring event.
4. We also select an end date for the review, in this case **_7_** days.
5. Select the user scope, in this case: **_All users and groups._**

![](/assets/images/image-1.png)

Next, we need to specify some more parameters.

![](/assets/images/image-2.png)

1. Select the role that you want to review.
2. Select the assigment type. You can pick between active or eligible assigments, or select both.
3. In this case we select self-review, but you can also select manager, or specific users.
4. User's access that was denied will be removed from the resource after the review completes if you set this to **_Enable_**.
5. Configure what should happen when the reviewer does not respond. We will take recommendations, meaning that users that not have been signed in frequently will be denied.

Optionally, you can configure advanced settings to show recommendations, require context, or set up reminders and mail notifications. I will leave this at the default values.

![](/assets/images/image-3.png)

The last step is to click **Start.** Since we have picked today as our start day, the review will be immediately active. The reviewer can expect an email any moment now.

## Reviewer experience

Since we picked self-review, each user with an active or eligible assignment for Exchange administrator will receive an email to review their access.

![](/assets/images/image-4.png)

Allan is no longer part of the project and does no longer need the Exchange role, so he denies his request.

![](/assets/images/image-5.png)

This is also visible for the administrator who created the access review, and after the review is done, the results are applied automatically.

![](/assets/images/image-7.png)

## Quickstart

Now, if you are not using Privileged Identity Management yet, there is an easy way to create Access reviews for your current admin roles.

If you don't have P2 licenses, you can easily activate a 30 day trial for 100 licenses.

![](/assets/images/image-10.png)

From the Privileged Identity Management portal, go to Azure AD roles, and click **Discovery and Insights**. From there, you can review the standing access for highly privileged roles, and create an access review with one single click.

![](/assets/images/image-8.png)

This will create a self-review that will run one time for 30 days. This review will apply results automatically, but users that not respond will keep their access.

![](/assets/images/image-9.png)

## Wrap things up

Lots of organizations are struggling with Azure AD roles and stale/standing access. Sometimes, this has been slumbering for some years now, where the tenant is at high risk. Now, I'm aware that not all organizations have the P2 license, but I want to encourage you to give that another thought, at least for your admins or priority users out there. Remember that you can also try this out for 30 days for 100 users for free.  
  
I have written several posts on this subject, so if you want to learn more, please reach out to those.  
  
[Role Assignable Groups and Privileged Identity Management. - JanBakker.tech](https://janbakker.tech/role-assignable-groups-and-privileged-identity-management/)  
[Privileged Identity Management Discovery and insights - JanBakker.tech](https://janbakker.tech/privileged-identity-management-discovery-and-insights/)  
[Azure Active Directory Identity Governance – Privileged Identity Management - JanBakker.tech](https://janbakker.tech/active-directory-identity-governance-privileged-identity-management/)  
  
Stay safe!
