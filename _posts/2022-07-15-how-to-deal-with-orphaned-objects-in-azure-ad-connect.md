---
title: "How to deal with orphaned objects in Azure AD (Connect)"
date: 2022-07-15
categories: 
  - "entra"
coverImage: "pexels-a-koolshooter-5258251-scaled.jpg"
---

We have done hybrid identity for a couple of years now, and it looks like the vast majority is not going to change that soon. Over the past years, we had different tools to facilitate hybrid identity. When we started this journey, there was no Azure AD Connect. We used tools like Dirsync or FIM/MIM, and that was not always easy. Still today, proper management of Azure AD Connect can be quite complex.

## What's the issue?

Today we are going to look at orphaned objects in Azure AD, that previously were synced objects, but somehow got messed up. Now, there can be multiple reasons for an object to become orphaned, but the most common are:

- Issues caused by upgrades or migrations
- Issues with the SQL database or the MetaVerse
- Admins restoring synced users from Azure AD recycle bin
- Objects that are deleted from AD, and got disconnected
- Changes in Azure AD Connect configuration
- Objects have been moved over multiple OU's, domains, or forests
- Someone messed up the Sync Rules

Anyhow, I can imagine that some organizations will end up with a lot of objects in Azure AD that are no longer managed from on-prem AD. Orphaned objects can be easily overlooked and forgotten. These objects can be a potential target for hackers (since they might be still active) or can still have licenses assigned to them. Active Diretory is no longer your single source of truth.

## Phase 1 - Inventory

There are multiple ways to find disconnected objects in Azure AD. For the sake of this article, I will show the most common, so that you can pick the one that fits your needs.

#### Search Connector Space

On your Azure AD Connect Server, open the Synchronization Service Manager. On the Connectors tab, select your Azure AD connector, and click on **_Search Connector Space_** from the Actions menu.

![](/assets/images/image-12.png)

Next, select the **Pending Import** scope, and tick the **Add** checkbox to find the disconnected objects.

![](/assets/images/image-14.png)

In this demo environment, I had only 4 users that became disconnected (deleted them in AD, and restored them from AAD deleted users), but yours may look way worse :)

#### Powershell

The first method might give you an idea of the situation, but it won't give you any export actions to work with. With a few items that is no problem, but when you have to deal with large amounts of objects, this method is a lot better and quicker.

Last year, Microsoft released version 2.0.3.0 of Azure AD Connect. That version came with some handy new PowerShell cmdlets. One of them is **Export-ADSyncToolsAadDisconnectors**. This cmdlet executes the CSExport tool to export all disconnectors to XML and then takes this XML output and converts it to a CSV file with these columns: UserPrincipalName, Mail, SourceAnchor, DistinguishedName, CsObjectId, ObjectType, ConnectorId, CloudAnchor.

On your AAD Connect server, fire up PowerShell and import the ADSyncTools module:

```
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools.psm1"
```

To export all objects, simply run:

```
cd C:\temp
Export-ADSyncToolsAadDisconnectors 
```

You can also scope to specific object types like users, groups, devices, public folders, or contacts:

```
Export-ADSyncToolsAadDisconnectors -SyncObjectType "user"
Export-ADSyncToolsAadDisconnectors -SyncObjectType "group"
Export-ADSyncToolsAadDisconnectors -SyncObjectType "device"
Export-ADSyncToolsAadDisconnectors -SyncObjectType "contact"
Export-ADSyncToolsAadDisconnectors -SyncObjectType "publicfolder"
```

Running this cmdlets will give you a great overview of the current disconnected objects.

![](/assets/images/image-15.png)

#### Azure AD Dynamic Groups

Now, this might only work for disconnected users, but this is a quick way to do an inventory on orphaned users in Azure AD. Create a new dynamic group and use this as the query:

```
(user.onPremisesDistinguishedName -ne null) and (user.dirSyncEnabled -eq false)
```

![](/assets/images/image-16.png)

As soon as the group is filled, you can use the export feature to get an overview.

![](/assets/images/image-17.png)

#### csReporter

During my research, I found another gem that can generate great reports on Azure AD Connect or FIM data. You can download it here: [FIMTooler/csReporter: Connector Space reporting tool for MIIS/ILM/FIM/MIM/Azure AD Connect (github.com)](https://github.com/FIMTooler/csReporter)

You can run the tool from the obj/release folder.

![](/assets/images/image-45.png)

Select the connector and the data selection. This will generate the source file.

![](/assets/images/mstsc_gyituowuKm.png)

Next, you can drill down and create reports in HTML, CSV, and Excel formats. You can also select which attributes to include in the report.

![](/assets/images/image-46.png)

## Let's fix it!

Okay, now we have insights into the orphaned objects, let's see what we need to do with them. First of all, there might not be a single solution to apply to all of them. Depending on your history, there might be objects that got out of sync but are still active. In that case, you might need to re-attach them. This post will not cover that, but please reach out to this guidance from fellow MVP Sander that covers the hard-en soft match with Azure AD Connect. [Explained: User Hard Matching and Soft Matching in Azure AD Connect - The things that are better left unspoken (dirteam.com)](https://dirteam.com/sander/2020/03/27/explained-user-hard-matching-and-soft-matching-in-azure-ad-connect/)

It can also be as simple as adding the missing OU to the sync scope again. [Customize an installation of Azure Active Directory Connect - Microsoft Entra | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-install-custom#domain-and-ou-filtering)

If you are not sure if objects are still used, please use the [signInActivity resource type - Microsoft Graph beta | Microsoft Docs](https://docs.microsoft.com/en-us/graph/api/resources/signinactivity?view=graph-rest-beta)

Now, if none of the objects are used or needed anymore, you can clean them up from Azure AD.

#### Delete them manually

If you have a few objects to get rid of, just head over to the Office 365 admin center or Azure AD portal, and search for the orphaned object. This is the easiest way.

![](/assets/images/image-18.png)

#### Remove-ADSyncToolsAadObject

There is another handy PowerShell cmdlet that Azure AD Connect provides: **Remove-ADSyncToolsAadObject**.

This cmdlet deletes synced objects from Azure AD based on SourceAnchor and ObjecType in batches of 10 objects You can simply point to the CSV file that you generated earlier using Export-ADSyncToolsAadDisconnectors cmdlet.

But first, you need to edit the CSV file and update the header from the ObjjectType column. Change it to **SyncObjectType**.

![](/assets/images/image-19.png)

![](/assets/images/msedge_Gguim00uBz.png)

After prepping the CSV file, run the following command:

```
Remove-ADSyncToolsAadObject -InputCsvFilename .\20220713-142148_Disconnectors.csv -Credential (Get-Credential)
```

According to the docs, the credentials need to be of a Global Admin account, and MFA must be disabled (**_come on Microsoft!_**)

![](/assets/images/image-21.png)

This cmdlet also supports different types of parameters like _What-if_, so please check the [Microsoft documentation](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/reference-connect-adsynctools#remove-adsynctoolsaadobject) for that.

In order to support the Google-fu folks, I documented some of the errors that you might experience during these steps:

**_When MFA is enabled, you'll get this error message:_**

```
There was a problem processing the batch. Error Details: Exception calling "ExportDeletions" with "1" argument(s):
"AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you
must use multi-factor authentication to access '00000002-0000-0000-c000-000000000000'.
```

**_When you forgot to change the header of the column, you will get this error message;_**

```
There was a problem creating DeletionEntry list. Error Details: Cannot index into a null array.                     
```

**When users got recovered from the Azure AD recycle bin, this cmdlet won't work. (please keep reading)**

```
This error indicates that a deleted object was recovered from the recycle bin in Azure AD before Azure AD Connect was able to confirm its deletion. Please delete the recovered object in Azure AD to fix this issue.
```

#### PowerShell to the rescue

When the Remove-ADSyncToolsAadObject cmdlet is not working for you, there is one alternative that I'd like to share: Azure AD PowerShell.

Now, I'm _the_ worst PowerShell dude on the planet, but luckily I got some help from the community! (community rocks!) Thanks to [Patrick van den Born](https://twitter.com/pvdnborn) I created a simple straight-to-the-point script that will remove the objects using the Azure AD PowerShell module. You can simply point to the CSV file, and the script will take care of the rest. We are using the **_CloudAnchor_** property to trim the Azure AD GUID and feed them to the Azure AD module to remove the object.

![](/assets/images/image-22-1024x262.png)

```
<#
.SYNOPSIS
  Script to clean up orphaned objects in Azure Active Directory.
.DESCRIPTION
  This script can be used together with the CSV results from Export-ADSyncToolsAadDisconnectors from the Azure AD Connect module.
  This module can be found on your AADConnect installation folder: Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools.psm1"
  
  Use this script to remove the orphaned/disconnected objects from Azure AD. This script can handle users, groups, devices, and contacts.

.NOTES
  Version:          1.0
  Author:           Patrick van den Born | @pvdnborn
                    Jan Bakker | @janbakker_
  Creation Date:    07/2022
  Purpose/Change:   First Release
  
.EXAMPLE
  First, use Export-ADSyncToolsAadDisconnectors to get a CSV with all the orphaned objects in Azure AD. Then, change the $csvfile to your CSV source file.
  Also, don't forget to install the Azure AD powershell module first: Install-module AzureAD
#>

Import-module AzureAD
Connect-AzureAD

$csvfile = "C:\Temp\csvfile.csv"
$objects = Import-Csv -Path $csvfile -Delimiter ","

foreach ($object in $objects) {
    if ($object.ObjectType -eq "user") {
        Remove-AzureADUser -ObjectId $object.cloudanchor.substring(5)
    } elseif ($object.ObjectType -eq "group") {
        Remove-AzureADGroup -ObjectId $object.cloudanchor.substring(6)
    } elseif ($object.ObjectType -eq "device") {
        Remove-AzureADDevice -ObjectId $object.cloudanchor.substring(7)
    } elseif ($object.ObjectType -eq "contact") {
        Remove-AzureADContact -ObjectId $object.cloudanchor.substring(8)
    } else {
        Write-Host "No actions for ObjectType of $($object.ObjectType) specified in script"
    }

}
```

You can check the Azure AD audit logs to confirm the actions.

![](/assets/images/image-23.png)

## Wrap things up

In a hybrid envoriment you want to make sure that Active Directory is leading, and that all changes are done on-prem. I hope this post will help out a few administrators that might have to deal with this.

Resources:

[Azure AD Connect: ADSyncTools PowerShell Reference - Microsoft Entra | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/reference-connect-adsynctools#remove-adsynctoolsaadobject)  
[Azure AD Connect: Troubleshoot errors during synchronization - Microsoft Entra | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/tshoot-connect-sync-errors)

Stay safe and in sync!
