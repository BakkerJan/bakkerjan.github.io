---
title: "Temporary exclusions for Conditional Access using PIM for Groups"
date: 2024-07-03
categories: 
  - "entra"
  - "security"
image: "/assets/images/pexels-jeshoots-com-147458-714701-scaled.jpg"
---

Conditional Access include and exclude groups cannot be messed with. As we have seen in a [previous blog post](https://janbakker.tech/prevent-conditional-access-bypass-with-restricted-management-administrative-units-in-entra-id/), this will impact your security posture. But what if you need to create a legitimate escape for operational purposes? Take this use case, for example:

You've enforced phishing-resistant MFA for your admin accounts on all apps (_you are a rockstar!_). Still, some applications do not support MSAL/WAM, or you have legacy scripts that use Internet Explorer 11 for authentication, so your admins can only use a password + weak MFA, like TOTP or push notification.

In that case, you'll need to create a temporary escape so that your admins can bypass the Conditional Access rules for a short amount of time and fall back to traditional MFA.

![](/assets/images/image-21.png)

For this solution we are going to use a few things:

- Role assignable group to manage exclusions. We will not assign any roles, but choosing this type of group will prevent regular Group admins to mess with the groups. Only the Group owner, PIM admins and Global admins can change membership.

- We enroll the group in Entra PIM for groups with time-limited assignments, and approval (optional)

If you want to learn more on the relationship between role-assignable groups and PIM for Groups, read more here: [Privileged Identity Management (PIM) for Groups - Microsoft Entra ID Governance | Microsoft Learn](https://learn.microsoft.com/en-us/entra/id-governance/privileged-identity-management/concept-pim-for-groups#relationship-between-role-assignable-groups-and-pim-for-groups)

Let's take care of our exclusion group first. Create a new group in Entra, and make sure it is role assignable. Remember, this is not a requirement, just an extra layer of security.

![](/assets/images/image-20.png)

After the group is created, onboard the group to Microsoft Entra Privileged Identity Management (PIM) for Groups.

![](/assets/images/image-22.png)

Next, let's have a look at the settings.

![](/assets/images/image-24.png)

Select member, to edit the settings for member users.

![](/assets/images/image-25.png)

Adjust the settings to your needs. In this example, I want to make sure that the admin requesting access to the group, has already done phishing resistant MFA in the first place. I'd also like a justification, and an approval of another admin or manager. You can also adjust the assignment settings, or set up notifications if needed.

![](/assets/images/image-26.png)

Going back to the group properties blade, we can now decide who can request access to the group, by adding an eligible assignment.

![](/assets/images/image-27.png)

Select Member and add the eligible members to the group. This is the group that can request the role.

![](/assets/images/image-28.png)

The last step configures the assigment type and duration.

![](/assets/images/image-29.png)

We also need at least two conditional access policies. The first one is our baseline, and can be either scoped to all users, or the exclusion group, so it will enforce "traditional" MFA for all apps.

![](/assets/images/image-39.png)

The second one will enforce phishing resistant MFA for all apps, and has our new group excluded.

![](/assets/images/image-38.png)

## End-user experience

Now, lets have a look on how this works for Alex. He can now request temporary membership for the exclusion group via Entra Privilged Identity Management, PIM for Groups.

![](/assets/images/image-31.png)

But since we also used authentication context, Alex needs to authenticate with phishing-resistant authentication first.

![](/assets/images/image-30.png)

Alex is prompted to do phishing resistant authentication.

![](/assets/images/image-37.png)

Next, Alex needs to add a reason for his request, and the request is sent out to the approver.

![](/assets/images/image-32.png)

On the approver side, this is how it will look like.

![](/assets/images/image-34.png)

After approval, Alex is now added to the exclusion group for 1 hour, like requested. Alex is also notified via an email.

![](/assets/images/image-35.png)

We can also check the membership in the Entra portal.

![](/assets/images/image-36.png)

After one hour, Alex is removed from the exclusion group automatically.

![](/assets/images/image-40.png)

## Let's wrap up

As you can see, PIM for Groups in Entra is realy powerful. This is just one usecase, but there are many other usecases that can be solved like this. Think about just in time access to licenses, applications, resources, and more!  
  
Also, I did not pick this usecase by accident. There is always a blocker that will prevent you from going full phishing-resistant, but let that not be an excuse to linger. It's better to have 90% covered, and have some escapes, than postponing the transition all together, and use weak MFA. Start today, and eat your own dogfood.

Stay safe!
