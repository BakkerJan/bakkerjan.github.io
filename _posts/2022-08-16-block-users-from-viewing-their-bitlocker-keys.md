---
title: "Block users from viewing their BitLocker keys"
date: 2022-08-16
categories: 
  - "entra"
  - "intune"
image: "/assets/images/pexels-ron-lach-10473517-scaled.jpg"
---

This post is mainly focused on a new tenant setting, where you can prevent your end-users from viewing their Bitlocker keys. By design, your users can see Bitlocker keys from devices they own from the MyAccount portal. [My Account (microsoft.com)](https://myaccount.microsoft.com/device-list)

For some (large) enterprise organizations, this is an unwanted feature.

![](/assets/images/vmconnect_1fkZp83ARP.png)

## Changing the setting

Using PowerShell or Graph API, you can now disable this feature. This will apply to the entire tenant so that users without proper permissions will no longer see their Bitlocker keys.

```
Connect-MgGraph -Scopes Policy.ReadWrite.Authorization
$authPolicyUri = "https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy"
$body = @{
    defaultUserRolePermissions = @{
        allowedToReadBitlockerKeysForOwnedDevice = $false #Set this to $true to allow BitLocker self-service recovery
    }
}| ConvertTo-Json
Invoke-MgGraphRequest -Uri $authPolicyUri -Method PATCH -Body $body
# Show current policy setting
$authPolicy = Invoke-MgGraphRequest -Uri $authPolicyUri
$authPolicy.defaultUserRolePermissions
```

Since I'm a big fan of [Graph Explorer](https://aka.ms/ge), I will also show how you can easily change this setting from the browser. First, let's check the current value of the setting by running:

```
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
```

![](/assets/images/msedge_iwBZdQtZHF.png)

The setting _allowedToReadBitlockerKeysForOwnedDevice_ is set to **true** by default, meaning users without the BitLocker read permission will be unable to view or copy their BitLocker key(s) for their owned devices.

We can set this to **false** by running:

```
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
    "defaultUserRolePermissions": {
        "allowedToReadBitlockerKeysForOwnedDevice": false
    }
}
```

![](/assets/images/msedge_Gt4telSd9M.png)

After you change this setting, your end users will no longer see their Bitlocker keys.

![](/assets/images/vmconnect_NcaKx7H27O.png)

## Delegation

I will also share this nice feature that I was not aware of. You can create a custom role with Bitlocker read permissions and assign this on a per-device scope level (using Privileged Identity Management). This means you can create a role to see Bitlocker keys, and make the role eligible for your users, where they can only see the keys from the device in scope. You can also throw in an approval step, multi-factor authentication step-up, or business justification if needed.

First, create a custom role, and add the Bitlocker reader permissions. Edit the role settings if needed. (When your tenant is not onboarded to Privileged Identity Management, you can only do direct assignments) Custom roles require a P1 or P2 license. [Learn more](https://docs.microsoft.com/en-us/azure/active-directory/roles/custom-create).

![](/assets/images/image.png)

Next, assign the user, and add the device scope.

![](/assets/images/image-2.png)

When the user checks back at the portal, the Bitlocker key is visible again, while other users cannot see their keys.

![](/assets/images/vmconnect_1fkZp83ARP.png)

## Wrap things up

I hope this post inspired you to help out your customers or clients take control of their Bitlocker keys. While this feature is still in preview, I will update this post with relevant information when needed. If you have ideas or concerns, please let me know. Happy to chat.

Stay safe

Resources:

[Manage devices in Azure AD using the Azure portal - Microsoft Entra | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/devices/device-management-azure-portal#block-users-from-viewing-their-bitlocker-keys-preview)  
[Create custom roles in Azure AD role-based access control - Microsoft Entra | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/roles/custom-create)
