---
title: "A first look at Administrative Units and My Staff in Azure Active Directory"
date: 2020-06-20
categories: 
  - "entra"
  - "security"
tags: 
  - "administrative-units"
  - "admins"
  - "delegation"
  - "helpdesk"
  - "managers"
  - "mfa"
  - "my-staff"
  - "passwords"
  - "reset"
  - "roles"
  - "tickets"
coverImage: "amazed-formal-male-looking-at-laptop-screen-3760809-scaled.jpg"
---

Recently, Microsoft introduced [Administrative Units](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-administrative-units) in Azure Active Directory. At the time of writing, this feature is in preview. Today we take a first look at how this is going to help organizations managing users and groups in Azure Active Directory. But to understand why this feature is such a big deal, we need to know what the difference is between the "classic" Active Directory and the "modern" Azure Active Directory structure.

## Active Directory

To illustrate this, I created a high overview of the Active Directory structure as we know it for many years. Take a look at the picture below.

![](/assets/images/Active-Directory.png)

In Active Directory, users and groups are placed in Organizational Units(OU's). Administrators can have delegated access to specific OU's to do management tasks, such as password resets. Administrators cannot manage users outside of their delegated OU's.

## Azure Active Directory

If we look at Azure Active Directory, users and groups are not placed in separate containers or units. Administrators can manage **all** users and groups according to their roles. So, authentication and helpdesk administrators, for example, can reset passwords for **all** users (non-admins) within Azure Active Directory.

![](/assets/images/Azure-Active-Directory.png)

## Administrative units

So, what are Administrative units? The official documentation states: _"Administrative units allow you to grant admin permissions that are restricted to a department, region, or other segments of your organization that you define."_ Within Azure Active Directory we can now create barriers to restrict admin permissions. That's great!

![](/assets/images/Azure-Active-Directory-with-AU.png)

Administrative units are consistent with the [Zero Trust principles](https://www.microsoft.com/en-us/security/business/zero-trust) **Assume Breach** and **Least Privilege**. By using AU's you scope the permissions of the administrators which minimize the lateral movement of hackers. Administrators can only manage users within their scope.

## Create and manage Administrative Units

Now that we understand the purpose of AU's, let's create one. Head over to the Azure Active Directory admin center and select **Azure Active Directory** -> **Administrative Units**. Click the + Add button.

![](/assets/images/image-57.png)

In this example, I create a Administrative Unit for all the users that are based in The Netherlands.

![](/assets/images/image-59.png)

Now, pay attention! You might think you can add a **group of users** to the Administrative Unit, but this will only grant the admins to manage the _properties_ of the group itself, **not** the members of the group. It does **not** add the members of the group to the AU.

To add users to the AU, you'll need to add them in the **Users** tab. This can be done manually, or in bulk, with CSV upload. At first, I assumed that you simply could add your (existing) groups and even dynamic groups, but that is not supported at the moment. Even when you add dynamic groups in the group tab, the admin cannot manage this. Only static groups are supported.

![](/assets/images/image-70.png)

With that in mind, we can now add a couple of users to our Administrative Unit. Let's add a few.

![](/assets/images/image-71.png)

Next, we are going to add the admin roles to the support team. In the administrative unit, head over to the Roles and administrators tab. Pick the role that you want to assign.

![](/assets/images/image-64.png)

Select the users that you want to assign to the role.

![](/assets/images/image-63.png)

```
Roles and administrators also not support (dynamic) groups at the moment. You can only add separate user accounts per Administrative Unit. Users can have different roles in different AU's.  
```

Take note of the scope. The role is only applied within the scope, the **Amsterdam Office | NL** Administrative Unit in this case.

![](/assets/images/image-65.png)

## Admin experience

Let's see how this looks for one of our admins. As we can see, Allan can only manage users in the Amsterdam Office.

![](/assets/images/image-66.png)

When Allen tries to edit a user outside the selected AU, this edit button is greyed out. Also, Allan cannot reset the users' password.

![](/assets/images/image-74.png)

_**Take note:**_ Administrative units apply only to management permissions. In this case, Allan does also has a Global Security Reader role. This will allow him to see the sign-in logs of all users, but he cannot edit the users, because they are out of scope.

![](/assets/images/image-73.png)

1. The security reader role is not scoped, so Allan can see the sign-in logs for all users.
2. The user admin role is scoped to the Amsterdam AU, so Allan can only manage user properties in that unit.

![](/assets/images/image-75.png)

When Allan wants to manage Joni's account, who is a member of the Amsterdam | NL unit, this is allowed.

![](/assets/images/image-76.png)

## My Staff, your staff

Now, in the extension of this, I'd like to point out another new feature. It's all about decentralized user management and delegation. Meet: [**My Staff**](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/my-staff-configure)

My Staff is built on top of Administrative Units. With the use of My Staff, managers can easily do common tasks for the users in their scope. For now, managers can:

- Add and update phone numbers
- Reset passwords
- Enable phone sign-in

So this adds a couple more use cases to the use of Administrative Units. To give you some ideas:

- Teachers can reset passwords for their students.
- Team managers can add and edit phone numbers of their team to use for multi-factor authentication.

First, let's make sure your managers can access My Staff. You can enable this feature in the Azure Admin portal under **Azure Active Directory -> User settings**. Under the User **feature previews** section, click the **Manage user feature preview settings** setting. You can enable this feature for a group of users, or all users.

![](/assets/images/image-77.png)

```
Only users who've been assigned an admin role can access My Staff. If you enable My Staff for a user who is not assigned an admin role, they won't be able to access My Staff.
```

## My Staff user experience

In the first section of this blog, we provided Allan some admin roles in the Amsterdam unit. Assume that Allan is a branch office manager. He can now use the [My Staff portal](https://mystaff.microsoft.com/) to manage his team.

When Allan logs in, he gets an overview of all the Administrative Units in his scope.

![](/assets/images/image-79.png)

Within the unit, he can now see all the users.

![](/assets/images/image-80.png)

Allan has the user administrator role for this AU. That means that he can reset the password, but cannot add or edit the phone number. That setting is greyed out.

![](/assets/images/image-81.png)

Now, let's add the authentication admin role to Allan's account so that he can manage the phone number too.

![](/assets/images/image-82.png)

Allan can now reset passwords, and manage the phone numbers for his unit.

![](/assets/images/image-83.png)

Let's add a phone number to the user account of Delia.

![](/assets/images/image-84.png)

Now again, **pay attention**. The phone number that we have provided for the user, is not the phone number that is shown in the user properties. This is the phone number that the user can use for authentication, also known as strong authentication methods. We can check that using the Graph API by running _https://graph.microsoft.com/beta/users/{ID}/authentication/phoneMethods/_

![](/assets/images/image-85.png)

In the Azure portal, this number is stored in the Authentication section of the users' properties.

![](/assets/images/image-86.png)

Depending on your settings, the user can then use the phone number to sign in with SMS, perform multi-factor authentication, and perform self-service password reset.

The My Staff portal is mobile friendly. Take a look at this short video and see how easy and smooth a user password reset is done. No manual needed for this, this works really intuitive!

![](/assets/images/o9xWb6eksm.gif)

## Bonus

If enabled, managers could also enable phone sign-in for their users. This will give them a passwordless experience.

![](/assets/images/image-87.png)

![](/assets/images/image-88.png)

```
Note: I assumed that that the user with the authentication admin role could enable this setting. However, I did not see this setting in the My Staff portal. When I log in as a Global Admin, I could see and enable the setting. I've created a Github issue for this. 
```

## Licenses

Using administrative units requires an Azure Active Directory **Premium** license for each administrative unit admin and Azure Active Directory Free licenses for administrative unit members.

Each user that's enabled in the text message authentication method policy must be licensed, even if they don't use it. Each enabled user must have one of the following Azure AD or Microsoft 365 licenses:

- [Azure AD Premium P1 or P2](https://azure.microsoft.com/pricing/details/active-directory/)
- [Microsoft 365 (M365) F1 or F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
- [Enterprise Mobility + Security (EMS) E3 or E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) or [Microsoft 365 (M365) E3 or E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## Let's wrap up

Now, I'm honest, I did not get this at first. I start clicking around in the Administrative Unit section and figured it would support (dynamic) groups to add users and delegate permissions. When this did not work, I stepped back and did some RTFM-ing first. Then I figured out how it works.

In my opinion, when you start using this right now, this will give you quite some extra tasks to set up and maintain. You could integrate this in your onboarding process, and I would suggest you take a look at the Graph API for this. But for your current users, it would be better if you could use your existing (dynamic) groups to add them to the specific Administrative Unit. Also, the admin role assignment should support this.

I'm really happy with the **My Staff** portal so far. This could take away the burden from the helpdesk, especially for common tasks such as password resets and phone number changes.

Take a look at the documentation and FAQ before digging in. Remember, this is in preview, so it might change in the future.

[https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/roles-admin-units-manage](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/roles-admin-units-manage)

[https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/my-staff-configure](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/my-staff-configure)

[https://docs.microsoft.com/en-us/azure/active-directory/user-help/my-staff-team-manager](https://docs.microsoft.com/en-us/azure/active-directory/user-help/my-staff-team-manager)

[https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/my-staff-configure#conditional-access](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/my-staff-configure#conditional-access)

```
One thing I've seen, when you set the phone number for the user, is the following: When you have password reset registration policy in place, and you have configured more than 1 method, the user is not prompted to register for the second or third method. I've also created an issue on Github for that.
```
