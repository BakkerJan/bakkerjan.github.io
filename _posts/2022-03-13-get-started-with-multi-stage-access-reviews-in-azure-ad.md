---
title: "Get started with multi-stage access reviews in Azure AD"
date: 2022-03-13
categories: 
  - "entra"
image: "/assests/images/pexels-thirdman-5684562.jpg"
---

Access reviews, part of the Azure AD Identity Governance module, is a great feature to reduce the risk associated with stale access assignments. Administrators can use Azure Active Directory (Azure AD) to create access reviews for group members or application access.

Reviews can be delegated to group owners, managers, or specific users, but there is also the self-review option where users can review their own access. By default, this is a single-stage process. Now in preview, there is the option to create multi-stage access reviews. Administrators can create up to 3 stages, and the feature is currently limited to groups and applications only. Let's have a look!

## Why multi-stage?

Multi-stage reviews can be used to create Azure AD access reviews in sequential stages, each with its own set of reviewers and configurations. It supports multiple stages of reviewers to satisfy scenarios such as: independent groups of reviewers reaching quorum, escalations to other reviewers, and reducing burden by allowing for later stage reviewers to see a filtered-down list.

Admins can now specify what reviews should go to the next stage, for example only approved reviewees, or those who were marked as "Don't Know". This will bring more flexibility.

## How to create a multi-stage access review?

To start, create a new access review as you would normally do, and select the items to be reviewed. To create a new review, go to the [Identity Governance blade](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/Controls) -> Access reviews -> + New access review.

In this example, I will review the Role-based Access Group for SharePoint admins.

![](/assets/images/image.png)

In the next step, tick the box Multi-stage review. Now, configure what should happen in the first and second stage. In this example, I would let users do self-review, and after that, the group owner needs to review the second stage. For the purpose of this demo, I set the duration to 1 day each. As a fallback for the second stage, I picked Alex Wilber.

![](/assets/images/image-3.png)

Now, you could add another stage if you want, by clicking the **\+ Add a stage** button.

New to the wizard is the "reveal review results" option. When you tick this box, reviewers in stages two and three will be able to see the final decision from the stage prior. So in our case, the group owners will see the results from the self-review.

Next, select what reviewees will go to the next stage. You can choose between:

- All reviewees
- Approved reviewees
- Denied reviewees
- Not reviewed reviewees
- Reviewees marked as "Don't Know"

![](/assets/images/image-4.png)

In the next screen you can configure what should happen upon completion; this will also apply to single reviews. The new section is do define the decision helpers for the configured stages. This will give recommendations based on the last sign-in within 30 days. You can enable or disable per stage.

![](/assets/images/image-5.png)

In the last screen, add a name and description and click **Create**.

![](/assets/images/image-6.png)

On the overview page, you can keep track of the reviews, and also see what the current stage is. If needed, you can stop the current stage. If you stop the current stage, reviewers on the current stage will no longer be able to give responses. If there is a subsequent stage, it will begin immediately.

![](/assets/images/image-7.png)

Administrators can also see the details per stage, and send reminders once the stage is started.

![](/assets/images/image-8.png)

## End-user experience

Let's see how this looks from an end-user perspective. By design, users that are targeted in an access review will receive an email. But they can also see an overview of pending reviews using the [My Access](https://myaccess.microsoft.com/) portal.

![](/assets/images/image-9.png)

After Christie finished the review, I skipped to the next stage using the admin portal. This will kick off the second stage.

## Second stage - Group owner

Now, Adele is the group owner, and therefore receives an email to review the access.

![](/assets/images/image-10.png)

She can now see the results from the previous stage, and also the recommendations. Based on this information, she approves the access from Christie and denies the access for Debra, since she did not respond to the review, and also did not sign in for the past 30 days.

![](/assets/images/image-11.png)

In the details pane, Adele can check the history, and also change the current decisions, as long as the review is active.

![](/assets/images/image-12.png)

After the review is completed, Adele will also receive an email to notify her. This is optional.

![](/assets/images/image-13.png)

Administrators can also see the details in the Identity Governance blade in the Azure portal. Of course, detailed audit data is available as well. Based on the configured settings, Debra is automatically removed from the Role-based Access Group, and therefore also lost the SharePoint admin role.

![](/assets/images/image-14.png)

## Wrap things up

This is a very welcome addition to the Access reviews module in Identity Governance. Administrators will have more flexibility to meet complex business requirements.

If you want to learn more, check out the Microsoft documentation:

[Create an access review of groups and applications - Azure AD | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/governance/create-access-review)

If you want to try out the new feature for yourself, be aware of the following prerequisites:

- Azure AD Premium P2.
- Global administrator, User administrator, or Identity Governance administrator to create reviews on groups or applications.
- Global administrators and Privileged Role administrators can create reviews on role-assignable groups. For more information, see [Use Azure AD groups to manage role assignments](https://docs.microsoft.com/en-us/azure/active-directory/roles/groups-concept).

Stay safe!
