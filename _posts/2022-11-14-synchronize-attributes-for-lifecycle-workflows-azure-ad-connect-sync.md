---
title: "Synchronize attributes for Lifecycle workflows - Azure AD Connect Sync"
date: 2022-11-14
categories: 
  - "entra"
coverImage: "pexels-erik-mclean-7205475-scaled.jpg"
---

Azure AD Lifecycle Workflows can be used to automate the Joiner-Mover-Leaver process for your users. Previously, I wrote about a use case where you can use LCW to automate the issuing of a Temporary Access Pass for new joiners. [Automate issuing Temporary Access Pass for joiners with LifeCycle Workflows - JanBakker.tech](https://janbakker.tech/automate-issuing-temporary-access-pass-for-joiners-with-lifecycle-workflows/) There are still a lot of organizations that use hybrid identities, so today, we will talk about the prerequisites for using LCW in a hybrid environment using Azure AD Connect Sync.  
  
Do know that Azure AD Connect **_Cloud Sync_** already supports these attributes. If you are using Workday or SuccessFactors, please check out this article: [How to synchronize attributes for Lifecycle workflows - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/governance/how-to-lifecycle-workflow-sync-attributes#understanding-employeehiredate-and-employeeleavedatetime-formatting)

![](/assets/images/image-18.png)

Lifecycle Workflows are based on two attributes:

- employeeHireDate

- employeeLeaveDateTime

With the launch of Azure AD Connect Sync version **2.1.20.0**, both attributes can now be synced for hybrid scenarios. This blog post will show you how to achieve that. Make sure you are using Azure AD Connect Sync version **2.1.20.0** or higher before getting started.

## Sync rules

Since _employeeHireDate_ and _employeeLeaveDateTime_ are no native attributes in Active Directory, we need to identify an attribute in AD that can be used. In this example, I use msDS-cloudExtensionAttribute1 and msDS-cloudExtensionAttribute2.

Before adding the rules, make sure that you temporarily disable the scheduler by running this PowerShell cmdlet:

```
Set-ADSyncScheduler -SyncCycleEnabled $false
```

Next, we need to create 4 new custom synchronization rules that synchronize the Active Directory attribute to the attributes in Azure AD. Let's start with the inbound rules:

Create a new **inbound** rule, and name it '**In from AD - EmployeeHireDate**'

Connected system: **pick your on-prem domain**  
Connected System Object Type: **user**  
Metaverse Object Type: **person**  
Link Type: Join  
Precedence: Pick an available number, 200, **for example.**

![](/assets/images/image-1.png)

On the Scoping filter and Join rules screen, select Next. (Leave default)

Next, add a new transformation rule, and select **employeeHireDate** as the target attribute and **msDS-cloudExtensionAttribute1** (pick yours) as the source attribute.

![](/assets/images/image-2.png)

Next create the same inbound rule for the employeeLeaveDateTime.

Create a new **inbound** rule, and name it '**In from AD - EmployeeLeaveDateTime**'

Connected system: **pick your on-prem domain**  
Connected System Object Type: **user**  
Metaverse Object Type: **person**  
Link Type: **Join**  
Precedence: **Pick an available number, 203, for example.**

  

![](/assets/images/image-4.png)

On the Scoping filter and Join rules screen, select Next. (Leave default)

Next, add a new transformation rule, and select **employeeLeaveDateTime** as the target attribute and **msDS-cloudExtensionAttribute2** (pick yours) as the source attribute.

![](/assets/images/image-5.png)

That's it for the inbound rules. Now let's create the outbound rules as well:

Create a new **outbound** rule, and name it '**Out to Azure AD - EmployeeHireDate'**

Connected system: **pick your Azure AD domain**  
Connected System Object Type: **user**  
Metaverse Object Type: **person**  
Link Type: **Join**  
Precedence: **Pick an available number, 201, for example**  

![](/assets/images/image-6.png)

On the Scoping filter and Join rules screen, select Next. (Leave default)

Next, add a new transformation rule, and select **employeeHireDate** as both the target and source attribute.

![](/assets/images/image-7.png)

Do the same for the EmployeeLeaveDateTime attribute:

Create a new **outbound** rule, and name it **'Out to Azure AD – EmployeeLeaveDateTime**'

Connected system: **pick your Azure AD domain**  
Connected System Object Type: **user**  
Metaverse Object Type: **person**  
Link Type: **Join**  
Precedence: **Pick an available number, 204, for example**

![](/assets/images/image-9.png)

On the Scoping filter and Join rules screen, select Next. (Leave default)

Next, add a new transformation rule, and select **employeeLeaveDateTime** as both the target and source attribute.

![](/assets/images/image-10.png)

Enable the scheduler again by running:

```
Set-ADSyncScheduler -SyncCycleEnabled $true
```

## Setting the attributes

Now that we have prepped our sync rules, we can fill our customExtensionAttributes. The format to use for both attributes is: **yyyyMMddHHmmss.fZ**

For my test user I used _20221101010000.0Z_ as the hire date = November 1st, 2022, 1:00 AM  
The leave date _20241130210000.0Z_ represents November 30th, 2024, 9:00 PM

The timestamps are not chosen randomly. Please read about [the importance of time](https://learn.microsoft.com/en-us/azure/active-directory/governance/how-to-lifecycle-workflow-sync-attributes#importance-of-time) when setting these attributes.

![](/assets/images/image-12.png)

## Reading the attributes

The employeeHireDate can be read from the Azure portal. While we are here, I'd like to share a quick tip where you can hide the empty attributes (just like in good old Active Directory)

We can see that the Employee hire date attribute is set to Nov 1, 2022, 1:00 AM

![](/assets/images/image-17.png)

Now, where is the employeeLeaveDateTime attribute, you might ask? Well.... This attribute is protected for obvious reasons. It is also not shown in the Azure portal, but can only be looked up using Graph API. ([https://aka.ms/ge](https://aka.ms/ge))

To look up the value, ensure you have the correct permissions: **User-LifeCycleInfo.Read.All**  

![](/assets/images/ApplicationFrameHost_pHX72gtEsp.png)

![](/assets/images/ApplicationFrameHost_mdAlWTUtjq.png)

Next, run this API query to get both attributes:

```
GET https://graph.microsoft.com/beta/users/max.verstappen@40rwbj.onmicrosoft.com?$select=employeeHireDate,employeeLeaveDateTime
```

![](/assets/images/image-14.png)

## Next steps

With both attributes in place, you are now ready to start using the Lifecycle Workflows in Azure AD. At the moment of writing, you can leverage this feature to automate joiner and leaver tasks in your environment, such as adding/removing users to groups and managing licenses and account lifecycle.

The extension to Logic Apps will give you endless possibilities and the flexibility you need. See this example: [Automate issuing Temporary Access Pass for joiners with LifeCycle Workflows - JanBakker.tech](https://janbakker.tech/automate-issuing-temporary-access-pass-for-joiners-with-lifecycle-workflows/)  

## One last thing....

If you were really quick, and installed version **_2.1.19.0_** of Azure AD Connect Sync, here is a note for you:  
  
_Microsoft fixed a bug where the new employeeLeaveDateTime attribute was not syncing correctly in version 2.1.19.0. Note that if the incorrect attribute was already used in a rule, then the rule must be updated with the new attribute, and any objects in the AAD connector space that have the incorrect attribute must be removed with the "Remove-ADSyncCSObject" cmdlet, and then a full sync cycle must be run._

Here is an example:

```
$userCN ="CN=Max Verstappen,OU=Red Bull,OU=CloudSync,DC=janbakker,DC=tech"

$connectorName = "janbakker.tech"

$csObj = Get-ADSyncCSObject -DistinguishedName $userCN -ConnectorName $connectorName

Remove-ADSyncCSObject -CsObject $csObj -Force
```

Learn more:  
[How to synchronize attributes for Lifecycle workflows - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/governance/how-to-lifecycle-workflow-sync-attributes)  
[What are lifecycle workflows? - Azure Active Directory - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/governance/what-are-lifecycle-workflows)  
[Set employeeLeaveDateTime - Microsoft Graph | Microsoft Learn](https://learn.microsoft.com/en-us/graph/tutorial-lifecycle-workflows-set-employeeleavedatetime?tabs=http)  
  
Also, make sure you check out this article by fellow MVP Pim Jacobs: [Starting with brand new Azure AD Lifecycle Workflows – Part 1 – Identity Man (identity-man.eu)](https://identity-man.eu/2022/10/19/starting-with-brand-new-azure-ad-lifecycle-workflows-part-1/)  
  
Stay safe!
