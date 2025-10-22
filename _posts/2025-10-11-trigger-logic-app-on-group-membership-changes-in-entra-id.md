---
title: "Trigger Logic App on group membership changes in Entra ID"
date: 2025-10-11
categories: 
  - "entra"
  - "logic-apps"
  - "security"
coverImage: "pexels-tima-miroshnichenko-6612229-scaled.jpg"
---

A couple of years ago, I stumbled upon a neat Logic App / Power Automate connector that can respond to changes in group membership. [Act on group membership changes in Azure Active Directory - JanBakker.tech](https://janbakker.tech/act-on-group-membership-changes-in-azure-active-directory/)  
  
Today, I'd like to give it some more love, since this is a very powerful, but underrated and probably also unknown piece of magic that will help you in a lot of automation scenarios. Especially when you are responsible for IAM Governance and lifecycle, but probably for a ton of other use cases.

I'm talking about the [Office 365 Groups connector](https://learn.microsoft.com/en-us/connectors/office365groups/). Don't let the name fool you. It also supports Entra security groups! _(Microsoft Entra ID groups with the attribute "isAssignableToRole" are not supported for now)_

![](/assets/images/image-21.png)

The other good thing? It supports **managed identities**!

![](/assets/images/image-22.png)

So, imagine you have a use case where you want to trigger a Logic App when a user is added to a specific group. Let's build that.

First, create a new Logic app and add a system-assigned managed identity to it.

![](/assets/images/image-23.png)

Next, we need to grant the managed identity read permissions to group membership changes. For that, we need to add at least **GroupMember.Read.All** Graph API application permissions to the managed identity.

See [Graph API: Minimal permissions to read user group membership - merill.net](https://merill.net/2024/09/graph-api-minimal-permissions-for-user-group-data/) for some background.

As usual, I'll ask [Lokka](https://lokka.dev) to do that for me.

![](/assets/images/image-24.png)

Let's check if the permissions were set correctly.

![](/assets/images/image-25.png)

With the permissions set, let's design the Logic App. (can take up to 10 minutes before the permissions are replicated)

I'd like to start adding a parameter for the groupID, but that's optional. Here, we define the group we want to act on.

![](/assets/images/image-26.png)

Start by adding the groupID, and set the interval.

![](/assets/images/image-27.png)

Let's now add a new user to the group and see if it's picked up by the Logic App.

When a user is added to the group, the body looks like this:

```
"body": {
        "@odata.type": "#microsoft.graph.user",
        "id": "90086dc1-43a5-4a6e-897e-f8fb6ad420d3"
    }
```

When a user is deleted from the group, the body looks like this:

```
"body": {
        "@odata.type": "#microsoft.graph.user",
        "id": "90086dc1-43a5-4a6e-897e-f8fb6ad420d3",
        "@removed": {
            "reason": "deleted"
        }
    }
```

In both events, the body contains the userID from the affected user, which you can easily pick up for your logic.

![](/assets/images/image-29.png)

## Trigger Conditions

If you only want your Logic App to trigger for specific events, you can configure one or multiple conditional triggers:

Only trigger for **added** users:

```
@empty(triggerBody()?['@removed'])
```

Only trigger for **deleted** user:

```
@not(empty(triggerBody()?['@removed']))
```

![](/assets/images/image-30.png)

This will give you full control over the events.

![](/assets/images/image-31.png)

When neither users are added nor removed, the Logic App will be skipped by default

![](/assets/images/image-32.png)

## Let's wrap up

If you are looking for group-based trigger events, this connector might be a good fit.

It's way easier than some alternatives like [Graph API subscriptions](https://learn.microsoft.com/en-us/graph/api/subscription-post-subscriptions?view=graph-rest-1.0) or acting on the audit logs. Since it also supports managed identity, we also don't have to worry about using service accounts or secrets.

Take note of the current limitations and known issues, and see if it fits your needs.

[Office 365 Groups - Connectors | Microsoft Learn](https://learn.microsoft.com/en-us/connectors/office365groups/)

Stay safe!
