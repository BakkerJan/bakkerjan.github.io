---
title: "A love story about Role Based Access Control for Applications in Exchange Online, Managed Identities, Entra ID Admin Units, and Graph API"
date: 2023-11-15
categories: 
  - "entra"
  - "logic-apps"
  - "security"
coverImage: "pexels-heiner-217250-scaled.jpg"
---

I've learned something new today. Hear me out.

Up until now, sending emails using managed identities trough Graph API was a bit of a hassle. You needed to grant access using Graph API or Powershell first, but before you could do that, you needed to find the correct IDs for Graph API, the Managed Identity, and the permission itself. Lucky for us, Jan Vidar spoiled us with [this nice blog post](https://gotoguy.blog/2022/03/15/add-graph-application-permissions-to-managed-identity-using-graph-explorer/), which I used pretty often.

Next, you would end up with a managed identity that could send mail from **EVERY** mailbox in your tenant. In order to limit that, we needed to use [App Access Policies](https://learn.microsoft.com/en-us/powershell/module/exchange/new-applicationaccesspolicy?view=exchange-ps&preserve-view=true) in Exchange Online to restrict access to specific mailboxes. A cumbersome process if you ask me. I like things simple.

Today, I stumbled upon this note:

![](/assets/images/image-1024x86.png)

This change is also announced on the Microsoft Tech Community website: [Retirement of RBAC Application Impersonation in Exchange Online - Microsoft Community Hub](https://techcommunity.microsoft.com/t5/exchange-team-blog/retirement-of-rbac-application-impersonation-in-exchange-online/ba-p/4062671)

To quickly find out if your tenant is using the legacy access policies, run:

```
Get-ManagementRoleAssignment -Role ApplicationImpersonation -GetEffectiveUsers
```

Digging through the documentation, I found out that this 'new' feature supported both Entra ID Admin Units and Security Groups. Let me quickly explain what this feature does:

RBAC for Applications in Exchange Online allows admins to grant permissions to an application that's independently accessing data in Exchange Online. At the core of this system is the **_role assignment_** configuration, which expresses an admin's intent to allow a service principal or managed identity to access data. In this case, allowing a managed identity to send mail from specific mailboxes.

![](/assets/images/image-1.png)

A Role Assignment is built from three parts:

1. **Application**: Who is this policy for? In this case, the managed identity.

3. **Application Role**: What permissions? In this case, Application Mail.Send. ([See all the supported permissions](https://learn.microsoft.com/en-us/exchange/permissions-exo/application-rbac#supported-application-roles))

5. **Resource Scope**: to what mailboxes? In this case, we can use "built-in scopes" Admin Units and Security Groups in Entra ID.

Okay, that's it for the theoretic part. Now I will show you how it's done to make it more clear.

## Step 1. The Service Principal

Now, when I say "_Service Principal_", I'm talking about the Service Principal in Exchange Online. You should consider the Service Principal in Exchange Online to be a pointer to an **existing** Service Principal or Managed Identity in Microsoft Entra ID. Service Principals can't be created directly using Exchange Online.

For the purpose of this demo, I created a system-managed identity that is attached to my Logic App. You can use any new or existing managed identity or service principal in Entra ID to get started.

![](/assets/images/msedge_TCPIyP5P55.png)

Let's fire up PowerShell and connect to Exchange Online PowerShell. Make sure you have the correct permissions. The Organization Management role group has the delegating role assignment for the new Application RBAC roles. You need to be a member of the Organization Management role group to assign these permissions. Alternatively, you can use Exchange Online RBAC to grant delegating assignments to these application roles as you see fit. In Microsoft Entra ID, you need the Global Administrator or Exchange Administrator roles to assign these permissions.

To create the new Service Principal, you'll need the Application ID and Object ID from Entra ID.

![](/assets/images/msedge_sXKsENzb2l.png)

```powershell
Connect-ExchangeOnline

New-ServicePrincipal -AppId 4e91ac94-b9bf-47e0-a11a-f342938b97a6 -ObjectId faca44fa-870f-4a2b-a120-e38e2f71d9f3 -DisplayName "Mail.Send.Demo"
```

![](/assets/images/powershell_HdTKW41e3x-1.png)

## Step 2. The Resource Scope

Now that we have defined the Service Principal (points to our managed identity in Entra ID), we can focus on the resource scope. In other words: from what mailboxes can the managed identity send mail?

As stated in the intro, we can use both security groups and Admin Units for this. So in this demo, we will start by creating a new Admin Unit, and add our first user to it.

![](/assets/images/msedge_S12C5n58kj.png)

Grab the ID of the Admin Unit. In Exchange, you can quickly check if this Admin Unit exists by running:

```powershell
Get-AdministrativeUnit -Identity <id>
```

![](/assets/images/image-2.png)

If you don't want to use Admin Units or Security groups, you can also use the Exchange Online [filters](https://learn.microsoft.com/en-us/powershell/exchange/recipientfilter-properties?view=exchange-ps) to create a resource scope. Here's an example:

```powershell
New-ManagementScope -Name "MySharedMailbox" -RecipientRestrictionFilter "UserPrincipalName -eq 'sharedmailbox@mydomain.com' "
```

## Step 3. The Role Assignment

Now that we have both the Service Principal and the Scope, we can tie those together by creating a new Role Assignment. You'll need the ApplicationID of the Managed Identity and the ObjectID of the Admin Unit.

![](/assets/images/msedge_K5tEKs24TT.png)

![](/assets/images/msedge_fM96G5NH5v.png)

```powershell
New-ManagementRoleAssignment -App 4e91ac94-b9bf-47e0-a11a-f342938b97a6 -Role "Application Mail.Send" -RecipientAdministrativeUnitScope 34cc3cca-70dd-47c7-af03-87d40925aa0e
```

For an overview of the supported application roles, check this: [Role Based Access Control for Applications in Exchange Online | Microsoft Learn](https://learn.microsoft.com/en-us/exchange/permissions-exo/application-rbac#supported-application-roles)

If you used the filters to define the resource scope, use the -CustomResourceScope parameter:

```powershell
New-ManagementRoleAssignment -App 876e190f-e540-422c-8f15-3342b851acd3 -Role "Application Mail.Send" -CustomResourceScope "MySharedMailbox" 
```

## Step 5. Test

With everything in place, it's time to test using this cmdlet:

```powershell
Test-ServicePrincipalAuthorization -Identity 4e91ac94-b9bf-47e0-a11a-f342938b97a6 -Resource AdeleV
```

![](/assets/images/msedge_EkHpSZc4Mw.png)

After adding Alex Wilber to the Admin Unit, the result will look like this:

![](/assets/images/msedge_XJigjlba9o.png)

Bonus: you can also create Role Assignments for Entra ID Security Groups by referring to the objectID of the group.

```powershell
New-ManagementRoleAssignment -App 4e91ac94-b9bf-47e0-a11a-f342938b97a6 -Role "Application Mail.Send" -RecipientGroupScope b284b992-6bd2-4be6-b290-93382c371462
```

As Adele is only a member of the Admin Unit, the result will look like this:

![](/assets/images/image-3.png)

Important note: **Changes to app permissions are subject to cache maintenance that varies between 30 minutes and 2 hours depending on the app's recent usage. When testing configurations, the test command bypasses this cache. An app with no inbound calls to APIs will have its cache reset in 30 minutes, whereas an actively used app will keep its cache alive for up to 2 hours.**

## Now send that email!

With the use of Logic Apps, it's now fairly easy to send email using Graph API.

![](/assets/images/image-4.png)

```
POST https://graph.microsoft.com/v1.0/users/AdeleV@M365B806821.OnMicrosoft.com/sendMail

{
  "message": {
    "body": {
      "content": "<p>Hope this email finds you in good spirits! We've got a tale from the cryptic corners of the IT realm that'll make your API tokens tremble and your permissions quake. Picture this: a mail sent through the Microsoft Graph API, but wait for the twist – the managed identity involved had no permissions to Graph API whatsoever!.</p>",
      "contentType": "HTML"
    },
    "subject": "The Mysterious Mail: When Managed Identities Get Mischievous",
    "toRecipients": [
      {
        "emailAddress": {
          "address": "info@janbakker.tech"
        }
      }
    ]
  },
  "saveToSentItems": "false"
}
```

## Wrap things up

The best improvement here is that the managed identity **does not need** any application permission to Graph API in Entra ID, giving access to all mailboxes. Instead, we provide more granular access in Exchange Online, using existing Admin Units or Security Groups.

![](/assets/images/image-5.png)

It also works for shared mailboxes!

![](/assets/images/image-6.png)

Credits to [Tony Redmond](https://practical365.com/author/tony-redmondredmondassociates-org/) and [Vasil Michev](https://www.michev.info/author/vasil) for putting out these very detailed blog posts that brought me up to speed.

Also check out [this script](https://github.com/mardahl/PSBucket/blob/d619f7d8edaca0c5c472eab09784e42c61b09460/Invoke-ExoRBACForEntraIDApp.ps1) to enable RBAC permissions to an Entra ID App in Exchange Online, scoping access to a native Administrative Unit by Michael Mardahl.  
  
[Microsoft Launches RBAC for Applications for Exchange Online (practical365.com)](https://practical365.com/rbac-for-applications/)  
[ExO RBAC improvements #2: Support for administrative units - Blog (michev.info)](https://www.michev.info/blog/post/4287/exo-rbac-improvements-2-support-for-administrative-units)  
  
More info: [Role Based Access Control for Applications in Exchange Online | Microsoft Learn](https://learn.microsoft.com/en-us/exchange/permissions-exo/application-rbac)

Stay safe!
