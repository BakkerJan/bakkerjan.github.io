---
title: "Dynamic Administrative Units using on-prem Organizational Units"
date: 2022-05-31
categories: 
  - "entra"
coverImage: "pexels-serhat-yilmaz-7694464.jpg"
---

Gone are the days that I could start a workshop, session, or training with: "Do you know the big difference between Active Directory and Azure Active Directory? Azure AD is flat, where AD is not." Well...... If there is one thing I've learned over the last years: it's hard to do Zero Trust in a flat landscape. Lucky for us, we have Administrative Units nowadays.

I really love Administrative Units (AUs) as they bring scoping to Azure AD. Until recently, AUs could only be static, but now this feature is heavily integrated with dynamic groups, meaning we can [create AUs based on user and device attributes](https://docs.microsoft.com/en-us/azure/active-directory/roles/admin-units-members-dynamic). Today, we are going to look at one attribute in particular: **_onPremisesDistinguishedName_**

The public preview of dynamic administrative units now supports the onPremisesDistinguishedName property for users. This makes it possible to create dynamic rules which incorporate the organizational unit of the user from on-premises AD.

## How to find the attribute

The **_onPremisesDistinguishedName_** attribute can be found in on-prem AD, after enabling the advanced features in Active Directory Users and Computers.

![](/assets/images/image-38.png)

After enabling the feature, right-click on the OU -> Properties, and find the attribute using the attribute editor tab.

![](/assets/images/image-49.png)

If you don't have access to on-prem AD, you can also find the property by using the Graph API. Using [Graph Explorer](https://aka.ms/ge), we grab the properties from one of the users.

```
GET https://graph.microsoft.com/beta/users/sergio.perez@40rwbj.onmicrosoft.com 

OR

GET https://graph.microsoft.com/beta/users/40d32222-a805-4408-bec2-b159c914f4e8
```

![](/assets/images/image-43.png)

## Building the AU

Now let's build the AU. Head over to the Azure portal, and find the [Administrative Units](https://portal.azure.com/#view/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/~/AdminUnit) feature in Azure AD. Add a new one to get started.

![](/assets/images/image-40.png)

Give it a proper name and description, and don't assign any roles for now. Click **_Review + Create_** to finish the wizard.

![](/assets/images/image-41.png)

After the AU is created, go into the properties of the AU, and change the membership type to **_Dynamic User_**. Next, click **_Add dynamic query_**.

![](/assets/images/image-42.png)

Build the query by selecting onPremisesDistinguishedName as the property, using **_Contains_** as the operator. Capture the OU from the DistinguishedName property, and use this as your value.

![](/assets/images/image-47-edited.png)

```
(user.onPremisesDistinguishedName -contains "OU=Red Bull,OU=CloudSync,DC=janbakker,DC=tech")
```

![](/assets/images/image-45.png)

Now, wait a few minutes while your AUs are being provisioned based on the on-prem OUs....... Et voila!

![](/assets/images/image-46.png)

## Let's wrap up

I hope this article was helpful to you. I really like the dynamic groups' engine, and how it is intertwined with Administrative units.

Stay safe!
