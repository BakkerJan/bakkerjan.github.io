---
title: "Require MFA for Azure AD domain join and Device Registration"
date: 2021-04-04
categories: 
  - "entra"
  - "security"
coverImage: "pexels-alexander-suhorucov-6457540.jpg"
---

Today we take a look at a new feature in Azure Active Directory that brings more granularity to the MFA requirement for device registration and Azure AD domain join. Up until now this was a tenant-wide setting and could be either set on or off. Because this setting was having some caveats and causing some inconvenience for end-users, this setting was mostly disabled, despite the fact that this is not the recommended option.

![](/assets/images/image-1.png)

It is recommended to enforce MFA before a user can register or join their device to Azure AD. This ensures that compromised accounts cannot be used to add rogue devices to Azure Active Directory. This setting can be found in the [Device settings blade](https://portal.azure.com/#blade/Microsoft_AAD_Devices/DevicesMenuBlade/DeviceSettings/menuId/) in Azure Active Directory.

## More granularity

Microsoft released a new user action in Azure AD Conditional Access that ultimately replaces this previous setting. To see this new action, instead of selecting cloud apps, pick the User actions tab. Here you will find the new setting.

![](/assets/images/image-2.png)

Next, you can configure this user action for specific users, groups, or roles. You are also able to use some conditions like device platform and locations. Sign-in and user risk are also available. To give you some examples of what you can do:

- Require MFA for device registration from untrusted locations only
- Require MFA for device registration when user risk is medium or higher
- Require MFA for specific operating systems like Android or iOS

Currently, this user action only allows you to enable MFA as a control when users register or join devices to Azure AD. Other controls that are dependent on or not applicable to Azure AD device registration are disabled with this user action.

![](/assets/images/image-4.png)

```
Note: when you are using Conditional Access with this user action, the "original" device setting option should be set to No. 
```

![](/assets/images/image-3.png)

## User actions Conditional Access

This is the second user action that admins can use in Conditional Access. I wrote another blog post about the first user action "_Register security information"_. [Learn more.](https://janbakker.tech/require-trusted-location-for-mfa-and-sspr-registration/)

![](/assets/images/image-5.png)

## Learn more

To learn more about this new feature, check out the articles below. Keep note that this feature is currently in preview.

[What's new? Release notes - Azure Active Directory | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/whats-new#public-preview---new-user-action-in-conditional-access-for-registering-or-joining-devices)  
[Cloud apps or actions in Conditional Access policy - Azure Active Directory | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/concept-conditional-access-cloud-apps#user-actions)  
[How to manage devices using the Azure portal | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/devices/device-management-azure-portal#configure-device-settings)

Stay safe!
