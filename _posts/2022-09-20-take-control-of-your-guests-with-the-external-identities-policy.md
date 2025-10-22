---
title: "Take control of your guests with the External Identities Policy"
date: 2022-09-20
categories: 
  - "entra"
coverImage: "pexels-andrea-piacquadio-3771110-scaled.jpg"
---

Today we take a look at the brand new External Identities Policy in Azure AD. This new policy controls whether external users can leave the guest Azure AD tenant via self-service controls. By default, guests in Azure AD can leave your organization whenever they want, using the [MyAccount](https://myaccount.microsoft.com/organizations) portal.

![](/assets/images/image.png)

If you want to prevent this, a new policy is here that allows you to take control. It's a tenant-wide setting, which will apply to all guest users.

## Setting the policy

Before your start, make sure that your privacy information is set in Azure AD. Learn more: [Add your organization's privacy info - Azure Active Directory - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/active-directory-properties-area)

When this setting was first released, it could only be set using Graph API, so the easiest way to set the policy is using [Graph Explorer](https://aka.ms/ge). Since this is a tenant-wide setting, I'm pretty sure you'll need Global Admin rights, so make sure you have the proper permissions to see or update this policy. Keep an eye on the official docs to learn more: [externalIdentitiesPolicy resource type - Microsoft Graph beta | Microsoft Docs](https://docs.microsoft.com/en-us/graph/api/resources/externalidentitiespolicy?view=graph-rest-beta)

First, we'll take a look at the current/default settings of the policy by running:

```
GET https://graph.microsoft.com/beta/policies/externalIdentitiesPolicy
```

![](/assets/images/image-1.png)

The property **_allowExternalIdentitiesToLeave_** defines whether external users can leave the guest tenant. If set to _false_, self-service controls are disabled, and the admin of the guest tenant must manually remove the external user from the guest tenant. When the external user leaves the tenant, their data in the guest tenant is first soft-deleted and then permanently deleted in 30 days.

The property **_allowDeletedIdentitiesDataRemoval_** notifies Azure AD whether to clean up the user information about the external identity from the guest tenant when the user is deleted in their home tenant. So, as long as the guest account is active in the home tenant, it will stay active in the guest (your) tenant. When the account is deleted from the home tenant, the guest account will automatically be cleaned up. At the time of writing, this feature is not implemented. Changing the setting will do nothing.

You can update the properties by using the following API:

```
PATCH https://graph.microsoft.com/beta/policies/externalIdentitiesPolicy

{
  "allowExternalIdentitiesToLeave":false,
  "allowDeletedIdentitiesDataRemoval":true
}
```

![](/assets/images/image-2.png)

This setting can also be configured using the Azure portal. This can be found under Azure Active Directory -> External Identities -> External collaboration settings. You can find the toggle in the External user leave settings section.

Yes means that the end user can leave the organization without approval from the admin. No means that the end user will be guided to review the privacy statement and/or contact the privacy contact for approval to leave.

![](/assets/images/image-8.png)

## User experience - Guests trying to leave the tenant

So let's see what it looks like for the end user when they try to leave your tenant. To access the self-service page, the user will go to [My Account](https://myaccount.microsoft.com/), and then **Manage Organizations**.

![](/assets/images/image-4.png)

Here, the user will find the home tenant and all the guest tenants where the user is active as a guest. The user will then click the **Leave** button.

![](/assets/images/image-5.png)

The user is then prompted another time to accept the deletion of data and clicks **Leave**.

![](/assets/images/image-6.png)

When the admin has set the property allowExternalIdentitiesToLeave to false, the user is then prompted with this message:

**You'll need permission to leave**

_To get permission to leave this organization, send an email request to your admin or <tenant name> privacy contact._

![](/assets/images/image-7.png)

## Wrap things up

So, we have another toggle to configure! There might be some organizations out there waiting for this feature. It's good to have this level of control over external collaboration. With this nifty little setting, you can easily create your own Hotel California in Azure AD:

_"You can check out any time you like  
But you can never leave"_

Stay safe!
