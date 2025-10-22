---
title: "KB - mobile phone number not in sync Azure AD Connect"
date: 2022-05-01
categories: 
  - "entra"
  - "knowledgebase"
image: "/assets/images/pexels-cup-of-couple-7657741.jpg"
---

This is a knowledgebase item. Hope it helps you out someday.

## The issue

Some users reported that the mobile phone number in Azure Active Directory / Office 365 was different from the number in on-prem Active Directory. Even though these users were synced with Azure AD Connect, the mobile phone attribute was no longer in sync.

## Cause

After some investigation, it seemed that the affected accounts were previously edited with the [Set-MsolUser](https://docs.microsoft.com/en-us/powershell/module/msonline/set-msoluser?view=azureadps-1.0) cmdlet from the MSOnline PowerShell module. To make sure this was the cause, I reproduced the issue in my lab:

1. Created a new user in Active Directory.
2. Added the mobile phone attribute in Active Directory.
3. The number got synced to Azure Active Directory.
4. Ran the _Set-MsolUser -ObjectId <ObjectID> -MobilePhone '<Mobile phone number'_ cmdlet
5. In Active Directory, I changed the mobile phone number again to another value.

After step 4, the number no longer gets updated.

By the way, this was the outcome of the Twitter poll on this subject:

![](/assets/images/image.png)

## Solution

Unfortunately, this is currently not something that you can fix by yourself. After I raised the issue with Microsoft support, the issue was resolved by Microsoft engineers. They ran a script to reset the DirSyncOverWrite attributes. After this was done, the attributes were updated from Active Directory again.

There seems to be a new flag feature on its way where you as a tenant admin will be given a choice to disable the 'DirSyncOverrides', making Active Directory the true source of authority of the value. Until then, raise a ticket with Microsoft support to get this fixed.

## Good to know

The MSOnline module lets you change the mobile phone attribute on synced objects. The Azure AD module will not let you change that property on synced objects.

![](/assets/images/image-1.png)
