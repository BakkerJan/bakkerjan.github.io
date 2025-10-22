---
title: "Prevent Conditional Access bypass with Restricted Management Administrative Units in Entra ID"
date: 2024-07-02
categories: 
  - "entra"
  - "security"
coverImage: "pexels-matthiaszomer-845265-scaled.jpg"
---

Bypassing Conditional Access is easy. That's because _most_ Conditional Access policies rely on Entra ID Security Groups. Since Entra ID is very "flat" by default, every admin with [group management permissions](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/permissions-reference#groups-administrator) can add or remove members to **ANY** group.  

That's why we want to handle our include and exclude groups carefully. This idea does not stand alone and is also mentioned in this [marvelous article](https://www.cloud-architekt.net/restricted-management-administrative-unit/#azure-ad-configuration-items) by Thomas Naunheim. If you're new to Restricted Management Administrative Units, I suggest reading this blog post first. In this post, we look at the concept and the steps required to set up Restricted Management Administrative Units and eligible Group Administrators using Entra Privileged Identity Management.

Let's take a look at a typical Conditional Access policy. This policy will enforce MFA for all apps and will target a specific group.

![](/assets/images/image.png)

We also have an exclusion group for break-glass accounts and some service accounts.

![](/assets/images/image-1.png)

Group Admins who add or remove members are also changing the scope of the Conditional Access policy, which can cause a bypass. Let's prevent that from happening.

For this demo, we need a role-assignable group to delegate our permissions. We make Alex Wilber a member of this group. Alex is the only admin who can add or delete members of the Conditional Access policies. That's the goal.

![](/assets/images/image-18.png)

![](/assets/images/image-19.png)

Let's also build a new Admin Unit and make sure to select "Restricted management administrative unit." The new Admin Unit will now be created as **Restricted Management Administrative Unit** (RMAU), the star of today's show.

![](/assets/images/image-3.png)

For now, we **do not** assign any roles. We will do this later so that we can select role-assignable groups from the picker. For some reason, I can only choose _users_ from the wizard.

![](/assets/images/image-4.png)

With the new RMAU created, we add our Conditional Access include/exclude groups.

![](/assets/images/image-5.png)

After a short while, the groups should be visible from the UI.

![](/assets/images/image-6.png)

Now, let's assign the **_Groups Administrator_** role to our role-assignable group.

![](/assets/images/image-7.png)

For the purpose of this demo, we add an **_eligible_** assignment.

![](/assets/images/image-8.png)

Now select the role-assignable group that we created earlier (**_ControlPlaneAdmins \[Tier0\]_**)

![](/assets/images/IPTVSmartersPro_Sj9wDVZtMk.png)

The assignment will be permanent for now, but you can also choose a limited one.

![](/assets/images/image-10.png)

After adding the assignment, check of the role was added to the group, within the RMAU.

![](/assets/images/IPTVSmartersPro_TNViLRneN9.png)

## How to check if a group is managed by RMUA

From the Entra admin center you see a yellow banner saying the group is restricted, and as you can see, even the Global Administrator cannot add new members.

![](/assets/images/IPTVSmartersPro_5IkNif8ONZ.png)

We can also check the assigned Administrative Units.

![](/assets/images/image-13.png)

## Manage group membership via Entra Privileged Identity Management

Now that we have restricted the groups and assigned the Groups Admin role to the RMAU, we can have a look at the _other side_. I'm now signed in as **Alex Wilber**, who should be eligible to update the group memberships. Before activating the group admin role, Alex could not add or delete any members of the group.

![](/assets/images/image-17.png)

Now, Alex goes into Entra Privileged Identity Management and activates the Group administrator role on the RMAU.

![](/assets/images/image-15.png)

After the role is activated, Alex can add and delete members to the group.

![](/assets/images/image-16.png)

## Let's wrap up

As you can see, the Restricted Management Administrative Units feature is very powerful when combined with Entra Privileged Identity Management. Conditional Access include and exclude groups should be protected to avoid security bypasses.

Read more:  
  
[Protection of privileged users and groups by Azure AD Restricted Management Administrative Units - Thomas Naunheim (cloud-architekt.net)](https://www.cloud-architekt.net/restricted-management-administrative-unit/)  
[Restricted management administrative units in Microsoft Entra ID (Preview) - Microsoft Entra ID | Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/admin-units-restricted-management)
