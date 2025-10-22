---
title: "KB - Reset cross-tenant access policies back to the system default."
date: 2022-05-20
categories: 
  - "entra"
image: "/assests/images/jim-wilson-5QvsD0AaXPk-unsplash.jpg"
---

This is a knowledgebase item. Hope it helps you out someday.

## The issue

Changes have been made to the default settings in the Azure AD cross-tenant policies. You want to revert them, but there is no button in the Azure portal UI to do that (at the moment of writing this article)

## Solution

This can be done using the [Graph API](https://docs.microsoft.com/en-us/graph/api/crosstenantaccesspolicyconfigurationdefault-resettosystemdefault?view=graph-rest-beta&tabs=http). The easiest way is using Graph Explorer. So, how does that work?

Browse to [https://aka.ms/ge](https://aka.ms/ge), and make sure that you are signed in. First, check if you have consented to the **_Policy.ReadWrite.CrossTenantAccess_** permissions.

Then, use this API request:

```
POST https://graph.microsoft.com/beta/policies/crossTenantAccessPolicy/default/resetToSystemDefault
```

![](/assets/images/image-20.png)

If successful, this action returns a 204 No Content response code. **{}**

The default settings are now restored:

**_Inbound access settings_**

| **Type** | **Applies to** | **Status** |
| --- | --- | --- |
| B2B collaboration | External users and groups | All allowed |
| B2B collaboration | Applications | All allowed |
| B2B direct connect | External users and groups | All blocked |
| B2B direct connect | Applications | All blocked |
| Trust settings | N/A | Disabled |

**_Outbound access settings_**

| **Type** | **Applies to** | **Status** |
| --- | --- | --- |
| B2B collaboration | External users and groups | All allowed |
| B2B collaboration | Applications | All allowed |
| B2B direct connect | External users and groups | All blocked |
| B2B direct connect | Applications | All blocked |

More info: [crossTenantAccessPolicyConfigurationDefault: resetToSystemDefault - Microsoft Graph beta | Microsoft Docs](https://docs.microsoft.com/en-us/graph/api/crosstenantaccesspolicyconfigurationdefault-resettosystemdefault?view=graph-rest-beta&tabs=http)

Stay safe!
