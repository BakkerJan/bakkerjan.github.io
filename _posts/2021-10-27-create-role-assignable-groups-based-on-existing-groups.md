---
title: "Create Role Assignable Groups based on existing groups"
date: 2021-10-27
categories: 
  - "entra"
  - "security"
image: "/assests/images/pexels-andrea-piacquadio-3831873.jpg"
---

Today's post is about Role Assignable Groups. Are you new to this? Please check out [this post](https://janbakker.tech/role-assignable-groups-and-privileged-identity-management/) first. If you are already familiar with Role Assignable Groups, you might know, these types of groups have an immutable property " _isAssignableToRole_" that cannot be changed. Therefore, you cannot convert existing groups into Role Assignable Groups and vice versa. Role Assignable Groups do not support group nesting, and the group cannot be dynamic as well.

So you have two options:

1. Start from scratch and add members manually
2. "Clone" existing groups and copy the members

Today, we create a single Power Automate flow to provide both options. [Microsoft provides a PowerShell script](https://docs.microsoft.com/en-us/azure/active-directory/roles/groups-create-eligible#copy-one-groups-users-and-service-principals-into-a-role-assignable-group) to copy members from existing groups, so if you prefer that method you can use this script:

```
#Basic set up
Install-Module -Name AzureAD
Import-Module -Name AzureAD
Get-Module -Name AzureAD

#Connect to Azure AD. Sign in as Privileged Role Administrator or Global Administrator. Only these two roles can create a role-assignable group.
Connect-AzureAD

#Input variabled: Existing group
$idOfExistingGroup = "14044411-d170-4cb0-99db-263ca3740a0c"

#Input variables: New role-assignable group
$groupName = "Contoso_Bellevue_Admins"
$groupDescription = "This group is assigned to Helpdesk Administrator built-in role in Azure AD."
$mailNickname = "contosobellevueadmins"

#Create new security group which is a role assignable group. For creating a Microsoft 365 group, set GroupTypes="Unified" and MailEnabled=$true
$roleAssignablegroup = New-AzureADMSGroup -DisplayName $groupName -Description $groupDescription -MailEnabled $false -MailNickname $mailNickname -SecurityEnabled $true -IsAssignableToRole $true

#Get details of existing group
$existingGroup = Get-AzureADMSGroup -Id $idOfExistingGroup
$membersOfExistingGroup = Get-AzureADGroupMember -ObjectId $existingGroup.Id

#Copy users and service principals from existing group to new group
foreach($member in $membersOfExistingGroup){
if($member.ObjectType -eq 'User' -or $member.ObjectType -eq 'ServicePrincipal'){
Add-AzureADGroupMember -ObjectId $roleAssignablegroup.Id -RefObjectId $member.ObjectId
```

If you are looking to use Power Automate/Logic Apps and Graph API instead, stay tuned for the next part.

## What is the usecase here?

Imagine having multiple groups to manage your administrators, or groups that represent your administrators. As stated in the intro, you cannot simply convert those groups into Role Assignable Groups. We have to be creative here.

We use Power Automate to grab those existing groups and create a new Role Assignable Group based on that existing group. You can choose to copy the members, or just create empty groups. Also, you can pick between security groups or Microsoft 365 groups (mail-enabled).

The process flow looks like this:

![](/assets/images/RoleAssignable-Groups.png)

Basically, we feed the flow with some existing group IDs. Then we provide some parameters for the new groups like name, description, and the group type. The flow will handle the groups based on the parameters. You can use different parameters for different groups. The flow can either create empty groups or also copy the members from the source groups to the new groups.

## The sourcefile

We start off with the source file. This file contains the information about the source groups and de parameters for the new groups. In my example, I placed it on a SharePoint site, but you can also use Onedrive for that. As long as Power Automate can reach the file location.

You can download a [copy of the source file](https://github.com/BakkerJan/Power-Automate/blob/master/Role%20Assignable%20Groups/MyCurrentAdminGroups.xlsx) as your starting point. Otherwise, create your own file, but make sure that you use the exact column titles and values.

![](/assets/images/image-29.png)

1. Enter the ID of the (source)group that you want to duplicate.
2. If you want to copy the members of the sourcegroup set to TRUE. Otherwise set to FALSE if you want to create empty role assignable groups.
3. Select the grouptype of the targetgroup, either **Security** or **Microsoft365** group. (copy those exact values, otherwise the flow will fail).
4. Enter a mailNickname for the targetgroup. This propery is required for every scenario.
5. Enter a description for the targetgroup.
6. Enter the name for the new targetgroup.

## App registration

Before we can create the groups, and add members to them we need API permissions. The easiest way to do that is to create an app registration in Azure AD with the **RoleManagement.ReadWrite.Directory** application permissions. Don't forget to grant admin consent for your tenant after adding the permissions.

![](/assets/images/image-31.png)

Next, capture the tenant ID, application (client) ID, and secret. We need that for the HTTP connector later on.

![](/assets/images/image-32.png)

## Go with the flow!

Now on to the flow. I'm not going through every little detail, but I will point out the major steps. You can grab the flow from Github to take a closer look yourself.

![](/assets/images/image-33.png)

1. Here we point to the excel source file that we created earlier. It will just list all the rows in the file. As you can see the flow is manually triggered.
2. For later use, we initialize a variable for the group ID. We use that to add the members to the group.
3. In case you have group naming policies in place, you can add the prefix that needs to be in front of your Microsoft 365 groups. If you don't have that, please leave this blank.
4. Same as the prefix, but now with the suffix. You can leave this blank if you don't have naming policies in place.

In the next stage, we create the actual groups, based on the information from the source file.

![](/assets/images/image-35.png)

1. For each row, we grab the Group information of the source group. This is optional since we also provide this information in the sourcefile. This will give you just a little more metadata to work with.
2. Here we create a switch to determine if we need to create a security or a Microsoft 365 group. This will depend on the GroupType value in the source file.
3. This case will take care of the security group; compose the body, send the POST HTTP request, and grab the ID of the new created group.
4. Same goes for Microsoft 365 group, but this one has a slighty different body.

In the next stage, we add the members to the groups.

![](/assets/images/image-36.png)

1. Depending on the value in the CopyMembers column, the flow will either copy the members from the source group or not.
2. If TRUE, the members are collected from the source group, and added to the new group.
3. Since Role Assignable Groups cannot contain nested groups, the array is filtered to only grab the users and user principals.
4. HTTP request will loop through all the members and adds them to the new group.
5. When the CopyMembers value is FALSE, no members are added to the group. So you can also use this flow to create empty groups, based on the source file. Just leave the CurrentGroupID value blank, and you are good to go.

NOTE: it is possible to add the members (or owners) directly when you create the group, but this will only support 20 users at the time, so you have to use batches. If you have less than 20 members in your source groups, you can use the following example:

```
POST https://graph.microsoft.com/beta/groups
Content-Type: application/json

{
    "description": "Group assignable to a role",
    "displayName": "Role assignable group",
    "groupTypes": [
        "Unified"
    ],
    "isAssignableToRole": true,
    "mailEnabled": true,
    "securityEnabled": true,
    "mailNickname": "contosohelpdeskadministrators",
    "owners@odata.bind": [
        "https://graph.microsoft.com/beta/users/99e44b05-c10b-4e95-a523-e2732bbaba1e"
    ],
    "members@odata.bind": [
        "https://graph.microsoft.com/beta/users/6ea91a8d-e32e-41a1-b7bd-d2d185eed0e0",
        "https://graph.microsoft.com/beta/users/4562bcc8-c436-4f95-b7c0-4f8ce89dca5e"
    ]
}
```

## Caveats

Couple of things to take note of:

- The mailNickname property is required for every role assignable group.
- The mailNickname property must have a unique value.
- You cannot add Service Principals to a Microsoft 365 group. So if your source group contains service principals, choose Security as your GrouType or remove the service principals from the source group first.
- In case you have group naming policies in place, please use the prefix and suffix variables to add them to the Microsoft 365 groups. I've noticed that this will only work if you have defined static prefix or suffix. Using attributes will be ignored by the Graph API. If you use security groups only, this case is not applicable, just leave the prefix and suffix empty.

## Entire flow

If it has value to you, check out this screenshot from the entire flow. You can also import the source file and flow into your own environment to take a closer look. Grab them from here: [Power-Automate/Role Assignable Groups at master · BakkerJan/Power-Automate (github.com)](https://github.com/BakkerJan/Power-Automate/tree/master/Role%20Assignable%20Groups)

![](/assets/images/1635346932.png)

To use this flow in your own tenant, use the Import button to import the [ZIP file](https://github.com/BakkerJan/Power-Automate/blob/master/Role%20Assignable%20Groups/CreateRoleAssignableGroupsBasedOnExistingGroups_20211027150750.zip). Point to the source file, and make sure all the dynamic values are correct. Create the app registration in Azure AD, and update the HTTP connector with your app/tenant id and secret.

![](/assets/images/1635405797.png)

## A few more words

I hope you learned a thing or two out of this. I strongly suggest [downloading the sample from Github](https://github.com/BakkerJan/Power-Automate/tree/master/Role%20Assignable%20Groups), and exploring the flow, maybe even rebuilding it in your own tenant. Use this blog as a reference, but I encourage you to **_make it better_** than this example. Did I overlook some use cases? Let me know!

Stay safe!
