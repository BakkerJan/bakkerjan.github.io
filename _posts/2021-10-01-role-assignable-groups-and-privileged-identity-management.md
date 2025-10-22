---
title: "Role Assignable Groups and Privileged Identity Management."
date: 2021-10-01
categories: 
  - "entra"
  - "security"
coverImage: "pexels-christina-morillo-1181317-1.jpg"
---

I have used this feature from the very beginning, but now that it [reached GA](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/whats-new#general-availability---assign-roles-to-azure-active-directory-ad-groups) (General Availability) in August 2021, it seemed like a good moment to talk about Role Assignable groups, and how they can help on our Zero Trust adventure.

Before this feature existed, Azure AD roles could only be assigned to individual user accounts. Since only Global- and Privileged Role Administrators can assign roles, this is a very cumbersome process. Especially when you are working with larger organizations with lots of (new) hires. Also, having those high roles for such day-to-day tasks, is not recommended.

## What are Role Assignable Groups?

Let's switch gears first. What are Role Assignable Groups in the first place? Well, they are looking the very same as your typical Microsoft 365 or security group, but in this case, the group has an extra property: _isAssignableToRole_.

This property can only be set when you are creating the group, and cannot be changed afterward. I'll come back to that later. What else do you need to know? A maximum of 400 role-assignable groups can be created in a single Azure AD organization (tenant). Unfortunately, you cannot "upgrade" existing groups to role assignable groups. You'll have to create them from scratch.

## How to use?

So, what can I do with a role assignable group? With this new feature, you can create groups, and add one or multiple roles to them. All members of that group would get access to that role. By default, only Global Administrators and Privileged Role Administrators can manage the membership of a role-assignable group, but you can delegate the management of role-assignable groups by adding group owners. Both security and Microsoft 365 groups can be used.

![](/assets/images/RAG1-1.png)

## Administrative Units

Another cool thing: you can also use this together with Administrative units. [Ten things you should know about Azure AD Administrative Units - The things that are better left unspoken (dirteam.com)](https://dirteam.com/sander/2020/10/07/ten-things-you-should-know-about-azure-ad-administrative-units/)

![](/assets/images/RAG-AU.png)

## Standing access v.s. Just-in-time

Okay, but what if you don't want standing access on Azure AD roles? Well, here is the good news: you can also use Role Assignable Groups together with Azure AD Privileged Access. With this feature, you can leverage just-in-time access for members (and owners!) To use this, you'll have to onboard your groups into Privileged Identity Management. After you have done this, Priviliegd Access is now active on the group.

When you combine these features, users can either elevate themselves as members of the group, having time-based access to Azure AD roles, or the group owners can add them manually after elevating themselves as owners of the group.

The good thing here is: not only do you create just-in-time access (JIT), but you can also use approval workflows and multi-factor authentication in this process. A possible workflow could be like this:

![](/assets/images/RAG-PIM-1.png)

## Security

Role Assignable Groups are treated differently. This is to prevent the elevation of privilege. For example, group administrators could potentially add their account to a group that has higher privileged roles attached.

That's why only Global Administrators and Privileged Role Administrators can manage the membership of a role-assignable group, but as I said before, you can delegate the management of role-assignable groups by adding group owners. Also, _group nesting_ is not supported, and groups **cannot** be dynamic.

If you could use dynamic groups, it would also be possible for someone to edit the query and elevate their privilege that way. For example, if there was a dynamic group with the query _(user.department -eq "IT") and (user.jobTitle -eq "Exchange Administator")_, this could be edited to _(user.department -eq "IT") and (user.jobTitle -eq "Exchange Administator") or (user.userPrincipalName -eq "bad\_actor@domain.com")_.

Good to know: only a Privileged Authentication Administrator or a Global Administrator can create a role-assignable group, and can change the credentials or reset MFA for members and owners of a role-assignable group.

## Tutorial

With the background all set, let's see how to create a Role Assignable Group. The process itself is not complex. Create a new group, like always, but in this case, make sure that "Azure AD roles can be assigned to the group" is selected.

![](/assets/images/image-1.png)

After the group is created, you can add Azure AD admin roles to the group.

![](/assets/images/image-2.png)

Next, select the role that you would like to assign, and (if supported) the scope of the role. Here you could add the Administrative Unit. [Learn more about Administrative Units.](https://docs.microsoft.com/en-us/azure/active-directory/roles/administrative-units)

![](/assets/images/image-3.png)

If you want to onboard the group into Privileged Access Management, this is an easy process. Just click on the "Enable privileged Access" button in the Privileged Access section of the group properties. The group is now onboarded.

![](/assets/images/image.png)

After the group is onboarded, you can also manage the member and owner settings of the group.

![](/assets/images/image-4.png)

## Automation

You can also use Graph API to create role assignable groups. Note that the property "IsAssignableToRole" is set to true.

```
POST https://graph.microsoft.com/beta/groups
{
  "description": "This group is assigned to Helpdesk Administrator built-in role of Azure AD.",
  "displayName": "Contoso_Helpdesk_Administrators",
  "groupTypes": [
    "Unified"
  ],
  "isAssignableToRole": true,
  "mailEnabled": true,
  "securityEnabled": true,
  "mailNickname": "contosohelpdeskadministrators",
  "visibility" : "Private"
}
```

## Wrap things up

As you can see, these features are very intertwined. I love to see how everything comes together, and the use-cases are endless. In this example, we were focused on Azure AD roles, but you can apply this concept to many more solutions. Think about time-based access to highly sensitive data for example.

Using this feature requires an Azure AD Premium P1 license. To also use Privileged Identity Management for just-in-time role activation, requires an Azure AD Premium P2 license. You can activate P2 trial license with a few clicks. I suggest you do this in a test/dev tenant.

![](/assets/images/image-5.png)

Learn more:

[Use Azure AD groups to manage role assignments - Azure Active Directory | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/roles/groups-concept)  
[Administrative units in Azure Active Directory | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/roles/administrative-units)  
[Set up self-service group management - Azure Active Directory | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/enterprise-users/groups-self-service-management)

Also, take note of the current [known issues](https://docs.microsoft.com/en-us/azure/active-directory/roles/groups-concept#known-issues).

Stay safe!
