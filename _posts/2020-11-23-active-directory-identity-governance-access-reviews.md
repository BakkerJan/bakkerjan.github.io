---
title: "Azure Active Directory Identity Governance - Access Reviews"
date: 2020-11-23
categories: 
  - "entra"
  - "security"
tags: 
  - "access"
  - "access-review"
  - "admins"
  - "azure-ad"
  - "guest"
  - "premiump2"
  - "renew"
  - "roles"
coverImage: "pexels-andrea-piacquadio-842921-scaled.jpg"
---

In this series, we take a look at Azure Active Directory Identity Governance. This premium feature provides you with all the tools that you need to take and keep control over your (external) identities and access to roles, resources, applications, and groups. In short, Identity Governance gives you three ways to do this:

- **[Azure AD Access Reviews](https://janbakker.tech/active-directory-identity-governance-access-reviews/)** (review membership of groups and access to applications) _(This blog post)_
- **[Azure AD Privileged Identity Management](https://janbakker.tech/active-directory-identity-governance-privileged-identity-management/)** (manage time-based and approval-based role activation to protect your resources with just-in-time and just-enough privileged access)
- **[Azure AD Entitlement Management](https://janbakker.tech/azure-active-directory-identity-governance-azure-ad-entitlement-management/)** (manage identity and access lifecycle at scale, by automating access request workflows, access assignments, reviews, and expiration)

## Licensing

Your directory needs at least as many Azure AD Premium P2 licenses as the number of employees who will be performing the following tasks:

- Member and guest users who are assigned as reviewers
- Member and guest users who perform a self-review
- Group owners who perform an access review
- Application owners who perform an access review

I'm not going into any detail, but please see [this section](https://docs.microsoft.com/en-us/azure/active-directory/governance/access-reviews-overview#license-requirements) for more information on licensing.

## What are access reviews, and why are they important?

With the use of access reviews, you can review the access of your users on regular basis, once per month for example. You can create reviews based on standing groups or teams. With the wide variety of settings, you can tweak the configuration to your needs.

Knowing that the users' identity is the new perimeter, you should always verify whether the rights of your users are still valid. Having excessive access rights in place can lead to compromises. In most cases, IT can not decide whether users have the right permissions. Using access reviews you can leave this up to the group owners, or the user itself.

## Why use access reviews?

Let me give you three examples to explain why access reviews are so important and useful.

**Example 1**: You are an IT administrator for your company, and it's your responsibility to create and maintain conditional access policies in Azure AD. Next to the policies, you have created multiple exclusion groups for your service accounts, test accounts, or users that you need to exclude for various reasons. Adding users to these groups is easy, but it's often forgotten to remove them from the groups when it's no longer needed.

**Example 2**: I'm a huge fan of dynamic groups in Azure AD. You can easily create groups based on user attributes so that if a user changes from role or department, the group membership is updated automatically. But there are some situations where _static_ groups are created and being used. Users are manually added by IT admins, group owners, or maybe even helpdesk staff. When a users' situation changes, the changes are often not reflected towards (Azure) Active Directory.

**Example 3**: Your organization allows guest users. Employees can invite guest users, and add them to teams, applications, and other internal resources. Guest users (B2B) is a really great feature in Azure AD, but if you don't govern those users, the situation is quickly running out of hand.

In these examples, access reviews would be the perfect solution to manage group memberships. We are going to work with example 3 for the rest of this article. Let's see how access reviews can manage your guest users. But first, some more information about access reviews.

## Types of access reviews

There are multiple types of access reviews. The easiest way to find them is by running the BusinessFlowTemplates API using the [Microsoft Graph Explorer](https://aka.ms/ge):

```
GET https://graph.microsoft.com/beta/businessFlowTemplates
```

![](/assets/images/1631-22-11-2020.png)

This will leave you with 5 types of access reviews. These templates are automatically created when the global administrator onboards the tenant to use the access reviews feature.

- Access reviews of guest user memberships of a group
- Access reviews of guest user assignments to an application
- Access reviews of assignments to an application
- Access reviews of memberships of a group
- Access reviews of memberships of an Azure AD role

## Guest users

As the usage of Microsoft Teams increased during the Covid-19 pandemic, so did external users. Guest users in Azure Active Directory (B2B) is an awesome feature to collaborate with users outside your organization. By default, users and guests in Azure AD can invite new guest users. So how do you keep this organized?

First step: **gain insight**. They easiest way is to create a dynamic group containing all your guest users. Create a new group, and create a dynamic membership rule like this:

![](/assets/images/image.png)

If you want better insight, Microsoft provided a [sample script](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse) that you can run in your Azure Active Directory. This script will give you a detailed report on your guest users, and also provides a script to automatically create groups for those users. That groups can be used for access reviews.

![How we suggest you use this script](/assets/images/ExternalIdentityUse.png)

## Create your first access review

So what does this looks like? In your Azure portal, head over to [Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/GettingStarted) \-> Access reviews and create a new review.

![](/assets/images/image-1.png)

In this example, I configure a one time access review for guest users in a specific group. You can use different parameters if you like.

![](/assets/images/1634-22-11-2020.png)

1. Pick a **name** for you review. You can use the name of the group or application that you want to review to make it more recognizable.
2. In this example, I want to do the review **just once**. You can also choose a different frequency.
3. Select **members of a group**.
4. In this example, we pick only the **guests**, but you can select all users.
5. Pick the **group** for the review. You can select multiple groups, but for each group, a separate review is created.
6. In the example, we let the guest do a self-review.

Next, configure the advanced settings. You can adjust the settings to your own needs.

![](/assets/images/1635-22-11-2020.png)

1. Select **Enable** to automatically apply the results. Select Disable to do it manually.
2. Select the behavior when the reviewers do **not respond**. If configure " take recommendations", the action depends on the users' activity in the past 30 days.
3. In case the user denies access, select what needs to be done. We choose to block the user first and remove it after 30 days.
4. Recommendations are shown to the reviewers to give insight into the users' activity. In the case of self-review, this does not make much sense, but if the reviewer is a group owner, this can give extra relevant information.
5. Users must enter a **reason for approval**.
6. **Mail notifications** are sent to the reviewers when the review starts, and to the admins when the review ends.
7. Reviewers that have not completed their review, can get a **reminder**.
8. You can add an **additional note** to the reviewer email. It is highly recommended to use this as an explanation for the end-users. What are you trying to achieve?

When the access review is created, the review shows as Active, and the emails to the reviewers are sent.

![](/assets/images/image-2.png)

So, what is happening at the reviewers' end? The user receives an email with the request for review. That will look something like this:

![](/assets/images/image-3.png)

The guest can select whether the access is still needed. The user must provide a reason before submitting.

![](/assets/images/image-4.png)

If needed, you can send out reminders to all the reviewers.

![](/assets/images/image-5.png)

As an admin, you can see all the results.

![](/assets/images/image-6.png)

Now, this review is still running for one more day, but since all the reviewers in this example responded, we can continue and stop the review to apply the actions.

![](/assets/images/image-7.png)

Once the review is stopped or applied, the status is changed to "**_Applying_**". In the meantime the guest account is disabled. The review will remain in the same status for 30 days until the user is deleted from the tenant.

![](/assets/images/image-8.png)

The guest user is blocked from sign-in, and does no longer have access to the tenant.

![](/assets/images/image-9.png)

## Wrap things up

Keep in mind that you can also [create access reviews for admin roles.](https://portal.azure.com/#blade/Microsoft_Azure_PIMCommon/ResourceMenuBlade/AccessReviews/resourceId//resourceType/tenant/provider/aadroles) This is part of Privileged Identity Management, which is covered in the next article in this series.

![](/assets/images/image-11.png)

As you play around with access reviews, you can have many different configurations for specific use-cases. Access reviews can give a lot of value to your organization. Together with the other premium features like entitlement management and privileged identity management, you stay in control of your groups, roles, and resources.

_A personal note. While working on this article, I noticed that there was one guest user in my tenant with the global admin role. I configured this only a few weeks back for a demo, but of course, I forgot about it. Now, this was only my demo tenant, and the guest was my very own colleague, but imagine this was a production tenant. We are all humans, and we cannot remember everything. Having access reviews on a recurring basis is highly recommended, especially for your admin roles. Also, let your users be in control of the groups and applications they own. not IT._

Stay safe!

More info:  
[What are access reviews? - Azure Active Directory | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/governance/access-reviews-overview)  
[Create an access review of Azure AD roles in PIM - Azure AD | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/privileged-identity-management/pim-how-to-start-security-review)  
[Use Azure AD Identity Governance to review and remove external users who no longer have resource access | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/governance/access-reviews-external-users)
