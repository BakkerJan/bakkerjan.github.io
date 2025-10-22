---
title: "How to restore deleted Entra ID Conditional Access policies and Named Locations"
date: 2025-10-06
categories: 
  - "entra"
image: "/assests/images/pexels-steve-850216.jpg"
---

Entra ID Conditional Access policies can now be restored for up to **30 days** after deletion. Next to deleted [users](https://entra.microsoft.com/#view/Microsoft_AAD_UsersAndTenants/UserManagementMenuBlade/~/DeletedUsers/menuId/), [groups](https://entra.microsoft.com/#view/Microsoft_AAD_IAM/GroupsManagementMenuBlade/~/DeletedGroups/menuId/Overview), and [apps](https://entra.microsoft.com/#view/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType~/null/sourceType/Microsoft_AAD_IAM), admins can now also restore deleted Conditional Access Policies, Trusted Locations, and Cross Tenant Access Policies. Learn more: [policyDeletableItem resource type - Microsoft Graph beta | Microsoft Learn](https://learn.microsoft.com/en-us/graph/api/resources/policydeletableitem?view=graph-rest-beta)

![](/assets/images/image-9.png)

This information can be found in the [Entra Admin Center](https://entra.microsoft.com/#view/Microsoft_AAD_ConditionalAccess/ConditionalAccessBlade/~/DeletedPolicies/menuId/Policies/fromNav/), but you can also access it directly through the Graph API.

![](/assets/images/image-4.png)

```
GET https://graph.microsoft.com/beta/conditionalAccess/deletedItems/policies
```

![](/assets/images/image-5.png)

Deleted policies can easily be tracked and restored from the Entra Admin Center:

![](/assets/images/image-10.png)

To restore the policy using Graph Explorer, use the policy ID of the deleted policy.

```
POST https://graph.microsoft.com/beta/conditionalAccess/deletedItems/policies/847f5c83-711c-4121-b556-75ab333ddd35/restore
```

![](/assets/images/image-6-1024x571.png)

## Restore Trusted Locations

Trusted Locations can also be restored from the Entra Admin Center.

![](/assets/images/msedge_NO80qo8Pbd.png)

Using Graph Explorer, you can list, restore, or permanently delete a soft-deleted Named Location.To list all deleted Named Locations for example, use:

```
GET https://graph.microsoft.com/beta/identity/conditionalAccess/deletedItems/namedLocations
```

![](/assets/images/image-8.png)

To restore a deleted Named Location using Graph Explorer:

```
POST https://graph.microsoft.com/beta/identity/conditionalAccess/deletedItems/namedLocations/1925365e-6689-4657-a429-e94871763fc7/restore
```

## Permanently delete soft-deleted objects

Soft-deleted objects, such as policies and named locations, can be permanently deleted.

Conditional Access policies and Named Locations can be deleted through the Entra admin center directly.

![](/assets/images/image-11.png)

## Deleted by (actor)

At the time of writing, information for deleted by (actor) or recently deleted policies is not yet available.

![](/assets/images/image-12.png)

This information can be found in the Audit Logs for the time being. Look for activity type:

- _Delete conditional access policy_

- _Delete named location_  
    

![](/assets/images/image-13.png)

The actor for the deleted Named Locations is already there.

![](/assets/images/image-15.png)

## (Bonus) How to prevent unwanted deletion of objects

Protected Actions, a feature of Entra ID, is capable of restricting the deletion of Conditional Access policies and Named Locations by applying Authentication Context to specific actions.

[What are protected actions in Microsoft Entra ID? - Microsoft Entra ID | Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/protected-actions-overview)

![](/assets/images/image-14.png)

## Let's wrap up

It's good to see that more and more objects in Entra ID are supported for soft-deletion, so that admins are able to restore them, if needed. To leverage the full potential, consider using the Graph API instead, as not all features are available in the Entra Admin Center (yet). [policyDeletableItem resource type - Microsoft Graph beta | Microsoft Learn](https://learn.microsoft.com/en-us/graph/api/resources/policydeletableitem?view=graph-rest-beta)

Stay safe!
