---
title: "Act on group membership changes in Azure Active Directory"
date: 2022-01-10
categories: 
  - "entra"
  - "power-platform"
coverImage: "pexels-francis-seura-802412.jpg"
---

Did you ever want to act on a change in group membership in Azure AD, for example, when a user is added to or removed from a specific group? I have found an easy way to do this with the use of Power Automate. You can use this for a lot of use-cases.

## What do we need?

For this solution, we use the [Office 365 Groups connector](https://docs.microsoft.com/en-us/connectors/office365groups/#when-a-group-member-is-added-or-removed) in Power Automate that holds the trigger: '_When a group member is added or removed_'. Now despite the connector being called Office 365 Groups (which should be renamed anyway), this will work with both Microsoft 365 groups and security groups in Azure AD. All we need is the ObjectId of the group. So this will be the trigger for our flow.

![](/assets/images/image.png)

Now, this feature is not documented very well, so to determine whether a user is added or removed we have to use an expression. The reason for this is the limited response when a user is **added**.

- ![](/assets/images/image-1.png)
    
- ![](/assets/images/1641818601.png)
    

So we are swooping in a condition and use the following expression:

```
empty(triggerBody()?['@removed']?['reason'])
```

When the result is true, the user is added, when the result is false, the user is deleted from the group.

![](/assets/images/image-3.png)

We also want to grab some details about the user and group, so that we can use that in our further steps. The flow will look like this:

![](/assets/images/image-5.png)

Now, in this case, we are sending an email to the affected user, but this can also be a chat message via Teams for example.

[Download](https://github.com/BakkerJan/M365Portal/blob/main/add-ons/GroupMembershipchanges_20220110125722.zip) the example from Github

More info on the connector: [Office 365 Groups - Connectors | Microsoft Docs](https://docs.microsoft.com/en-us/connectors/office365groups/)
