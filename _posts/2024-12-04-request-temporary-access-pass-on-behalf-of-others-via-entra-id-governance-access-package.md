---
title: "Request Temporary Access Pass on behalf of others via Entra ID Governance Access Package"
date: 2024-12-04
categories: 
  - "entra"
  - "security"
image: "/assests/images/Presentation2.png"
---

While looking at this new feature ([Request access packages on-behalf-of other users (Preview) - Microsoft Entra ID Governance | Microsoft Learn](https://learn.microsoft.com/en-us/entra/id-governance/entitlement-management-request-behalf)), an interesting use case popped to mind.

I thought of a scenario where a manager would request a Temporary Access Pass on behalf of a direct report so the IT service desk would not be needed. Apart from the details on how a manager would verify the identity of the team member, this seemed like a good proof of concept to test out. Here's the idea:

1. A manager would go to [https://myaccess.microsoft.com](https://myaccess.microsoft.com) and request a new access package on behalf of a direct report.

3. A custom extension will trigger a Logic App when this package is requested (and approved).

5. The Logic App will then create a new Temporary Access Pass for the user and send an SMS or email to the user.

![](/assets/images/Presentation2.png)

**_As this example is very effective and easy to set up, anyone with control over the manager attribute can request a Temporary Access Pass for non-admin users in Entra ID. This could potentially be used as an escalation path._**

## What do we need?

- Entra ID Governance license (or a free trial for 90 days).

- There are two users: a manager and a direct report. Be sure to set the manager property.

- A custom extension within the catalog (You'll also need an Azure subscription for the Logic App).

- A managed identity to create the TAP in Graph API.

- Any messaging services (SMS or email) to deliver the TAP.

## Create a new custom extension

Go to the Entra admin center and select your Identity Governance catalog. Then, add a new custom extension.

![](/assets/images/image-1.png)

I won't review all the steps, but here is the summary. This step will create a new Logic App in your selected Azure subscription.

![](/assets/images/image.png)

## Create the Access Package

With the custom extension prepped, we will now create a new Access Package. This will not grant access to any resources but will act as the shipping package and trigger the Logic App. Make sure the option "**_Allow managers to request on behalf of employees (preview)_**" is enabled. In this example, the package will be available for all users, but you could only make it available to a group of managers for example.

![](/assets/images/image-2.png)

You could also require approval, make the manager the approver, and select "**_Bypass approval stage if the manager is the requester and approver (preview)_**." This will skip the approval in this scenario.

![](/assets/images/image-25.png)

I use the lifecycle settings to delete the assignment after 1 hour. That won't impact the Temporary Access Pass lifetime, but will make sure a manager can request the package multiple times for one user if needed.

![](/assets/images/image-3.png)

Make sure to select the custom extension that we just created. Pick the stage you prefer. In this example, I select "**_Request is approved_**"

![](/assets/images/image-4.png)

## Create the managed Identity

We need an automation account to create the temporary access pass. The easiest way is to enable a system-managed identity on our Logic App (the one just created) and then assign the Authentication admin role to it.

![](/assets/images/image-5.png)

Copy the object ID and assign the role to the service principal.

![](/assets/images/image-6.png)

## Design the Logic App

Okay, with everything in place, we can now design the logic app. To create a new TAP on behalf of the affected user, use the dynamic value from the trigger to inject the objectID from the user. Use the documentation for more info: [Create temporaryAccessPassMethod - Microsoft Graph v1.0 | Microsoft Learn](https://learn.microsoft.com/en-us/graph/api/authentication-post-temporaryaccesspassmethods?view=graph-rest-1.0&tabs=http#http-request)

```
POST https://graph.microsoft.com/v1.0/users/@{triggerBody()?['Assignment']?['Target']?['ObjectId']}/authentication/temporaryAccessPassMethods

{
  "lifetimeInMinutes": 60,
  "isUsableOnce": false
}
```

![](/assets/images/image-8.png)

Make sure to select your managed identity as the authentication type and use https://graph.microsoft.com as the target audience.

I then used my Twilio account to send the TAP over SMS. I used a static value, but you could also make this dynamic by grabbing it from the Graph API or requesting the information from the access package, for example.

![](/assets/images/image-9.png)

## User experience

The manager can request the access package on behalf of his team members using https://myaccess.microsoft.com

The Logic App will then be triggered:

![](/assets/images/image-11.png)

Checo will then receive this text on his mobile phone:

![](/assets/images/image-10.png)

## Let's wrap up

I love to see how easy it is to integrate Identity Governance in all kinds of usecases. And I'm sure it will bring new ideas to the table once you see this example.

More info:  
[Request access packages on-behalf-of other users (Preview) - Microsoft Entra ID Governance | Microsoft Learn](https://learn.microsoft.com/en-us/entra/id-governance/entitlement-management-request-behalf)  
[Create temporaryAccessPassMethod - Microsoft Graph v1.0 | Microsoft Learn](https://learn.microsoft.com/en-us/graph/api/authentication-post-temporaryaccesspassmethods?view=graph-rest-1.0&tabs=http#http-request)  
[What is entitlement management? - Microsoft Entra ID Governance | Microsoft Learn](https://learn.microsoft.com/en-us/entra/id-governance/entitlement-management-overview)
