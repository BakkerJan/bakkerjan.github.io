---
title: "Review guest access across Microsoft 365 groups (teams)"
date: 2021-03-07
categories: 
  - "entra"
image: "/assests/images/pexels-andrea-piacquadio-3933690.jpg"
---

In a previous blog post I wrote about [Azure AD Access Reviews](https://janbakker.tech/active-directory-identity-governance-access-reviews/), and how they can help you in various use-cases. One of them is taking control over your guest accounts in Azure AD. You can select Microsoft 365 groups (or teams if you will) to review the current guest users. Microsoft released a new feature within Access Reviews to select **_all Microsoft 365 groups with guest users_**. Using this method, you don't have to create an access review for each group, but this will take care of all existing and new Microsoft 365 groups in your environment.

## What do we need?

Access Reviews is part of the Azure AD Identity Governance stack, meaning you need a premium P2 license for:

- Member users who are assigned as reviewers
- Member users who perform a self-review
- Member users as group owners who perform an access review
- Member users as application owners who perform an access review
- Guest users who are assigned as reviewers
- Guest users who perform a self-review
- Guest users as group owners who perform an access review
- Guest users as application owners who perform an access review

Now the license rules around guests can be a little fuzzy, but Azure AD uses either 1:5 ratio, which means you can have 5 guests for each premium license in your tenant, or Monthly Active Users (MAU) billing. The latter one gives you 50.000 free guest users per month. Before you can use the MAU billing, you have to link your subscription. [Learn more.](https://docs.microsoft.com/en-us/azure/active-directory/external-identities/external-identities-pricing)

![](/assets/images/image.png)

## Create your access review

Creating an access review is a very easy procedure. Head over to your Azure portal and select the [Identity Governance blade](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/GettingStarted). Next, select Access reviews. To get started, select **\+ New access review**

![](/assets/images/image-1.png)

In the next screen, select **Teams + Groups**, exclude the groups if needed, and click **Next**.

![](/assets/images/image-2.png)

Next, select the review method and frequency. In this case, I select the group owners as the reviewers, but you can also do a self-review, or select a specific user or group. In this example, all team/group owners have to review their guest users once a quarter. If needed, select (a few) fallback reviewers.

![](/assets/images/image-4.png)

On the next screen, select the settings that apply to your access review. In this example, the guest users are automatically removed if the owner of the team denies access. If no response is given, the guest users will also be removed. The reviewers will also get information about guest users that have not signed in for the past 30 days to help with the decision. Reviewers also need to add a justification and will be reminded if they don't respond.

![](/assets/images/image-5.png)

In the last screen, review your settings and select **Create** to create the access review.

![](/assets/images/image-6.png)

## Approvers experience

A few minutes after the creation of the review, team owners will receive an email when their teams contain guest users.

![](/assets/images/image-7.png)

Now, in this case, there is only one guest user on the Contoso demo team. Christie also sees that the user has not signed-in for more than 30 days, so denies access to the team. As long as the review runs, she can always change the decision using the My Access portal. [Learn more](https://janbakker.tech/self-service-in-microsoft-365/).

![](/assets/images/image-8.png)

Administrators can keep track of the results, stop the review to apply the changes, and see audit logs.

![](/assets/images/image-9.png)

## Let's wrap up

I really love this new feature for its simplicity. It's easy to set-up, and it keeps track of all your Microsoft 365 teams. A lot of organizations embraced Microsoft Teams for collaboration, and therefore a lot of guest users can be active in the tenant. This way you can clean-up your guest users, and remove stale accounts. Keep in mind that this is primarily meant for your member users, so it's a good idea to keep reviewing access for internal users as well.

[Azure Active Directory Identity Governance - Access Reviews - JanBakker.tech](https://janbakker.tech/active-directory-identity-governance-access-reviews/)  
[Use Azure AD Identity Governance to review and remove external users who no longer have resource access | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/governance/access-reviews-external-users)

Stay safe!
