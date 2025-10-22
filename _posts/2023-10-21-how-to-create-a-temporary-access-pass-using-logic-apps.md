---
title: "How to create a Temporary Access Pass using Logic Apps"
date: 2023-10-21
categories: 
  - "entra"
  - "logic-apps"
  - "security"
coverImage: "ed61929b-cc4f-4f98-aab6-e8362ea26da7.jpeg"
---

Now that more and more organizations are moving towards passwordless, a Temporary Access Pass becomes indispensable for onboarding and recovery. Using Logic Apps (or Power Automate), organizations can automate and integrate the creation of Temporary Access Passes in their current IT processes. Logic Apps can be triggered from customer service tools like ServiceNow or TOPdesk, to start fully automated workflows.

In this blog post, you will learn how to create a Temporary Access Pass in Entra ID using Logic Apps, Graph API, and a managed identity. Understanding the components involved is crucial in designing this process, as a Temporary Access Pass is a strong method that can also be used by attackers. Therefore, using passwordless service principals is a vital part of the process.

## Preparations

In the first step, we create the Logic App. As this will run on Azure, you need an Azure subscription and a resource group to start.

Search for Logic Apps in the Marketplace, and create a new Logic App. Pick the right subscription and resource group. Make sure you select the **consumption-based** plan type.

![](/assets/images/2023-10-21-000160.png)

After the Logic App is created, go to the new resource.

![](/assets/images/2023-10-21-000161.png)

First, we need a service principal representing this Logic App in Entra ID. Logic Apps can then use this account to run actions. This approach is entirely passwordless, so there is no need for traditional service accounts. Go to the Identity section and enable the system-assigned managed identity.

![](/assets/images/2023-10-21-000163.png)

Now that our identity is created, we must ensure it has the correct privileges to create a Temporary Access Pass. The easiest and safest way to do that is by adding the Entra ID admin role of Authentication Administrator. This role can create Temporary Access Passes for non-admin users.

Head over to the [Roles and Administrators](https://portal.azure.com/#view/Microsoft_AAD_IAM/RolesManagementMenuBlade/~/AllRoles/adminUnitObjectId//resourceScope/%2F) blade and add a new assignment.

![](/assets/images/2023-10-21-000164.png)

Now look for the new system-assigned managed identity you've just created in the Logic App. You can either search for the name of the Logic App or the objectID of the managed identity. This can be found in the Logic App identity section.

![](/assets/images/2023-10-21-000165.png)

## Building the Logic App

Now that we have prepped all the resources, we can configure the Logic App itself. Go to the Logic app designer and select "**_When a HTTP request is received_**" as the trigger.

![](/assets/images/2023-10-21-000168.png)

Next, add a new action and pick the **Data Operations** connector. Add the **Compose** action.

![](/assets/images/2023-10-21-000170.png)

We will use this property to feed the UserPrincipalName or ObjectID of the user to the Logic App. This will tell us for which user the Temporary Access Pass is created. Rename the Compose action to something you can easily recognize, and add the UPN or Object ID of a test user. Make sure the user is eligible to use a Temporary Access Pass. [Learn more](https://learn.microsoft.com/en-us/azure/active-directory/authentication/howto-authentication-temporary-access-pass#enable-the-temporary-access-pass-policy).

In this example, I added the UPN of my test user. We will need this property in the next step.

![](/assets/images/2023-10-21-000171.png)

The next action is the HTTP action. We will use this to "_talk_" to Graph API.

![](/assets/images/2023-10-21-000172.png)

Use the following properties for the HTTP action:

URI:

```
POST https://graph.microsoft.com/v1.0/users/userID/authentication/temporaryAccessPassMethods
```

Body:

```
{
    "lifetimeInMinutes": 60,
    "isUsableOnce": false
}
```

Make sure the properties in the body align with the settings in your tenant. In this example, we create a Temporary Access pass that is valid for 1 hour and can be used multiple times.

![](/assets/images/2023-10-21-000178.png)

Now, we need to replace the _userID_ part of the URI with the value of the compose action (UserPrincipalName) that we created earlier. As this might be a bit confusing, I've created a short video to demonstrate this action. If you select the userID value, you can then pick the output of the compose action from the dynamic content.

Next, we need to configure the authentication part. As the HTTP action will send data to the Graph API, it needs to have some sort of authorization to complete the action.

Add the authentication parameter to the HTTP action.

![](/assets/images/2023-10-21-000177.png)

Select "Managed Identity".

![](/assets/images/2023-10-21-000179.png)

Make sure the system-managed identity property is selected.

For the audience, use:

```
https://graph.microsoft.com
```

![](/assets/images/2023-10-21-000180.png)

Your HTTP action will look similar to this:

![](/assets/images/2023-10-21-000181.png)

## Save & test!

Now it's time to save the Logic App.

![](/assets/images/2023-10-21-000185.png)

After we have saved the Logic App, we can run and test it.

![](/assets/images/2023-10-21-000186.png)

Fingers crossed and wait for the result. If all goes well, there should be three green checkmarks.

![](/assets/images/2023-10-21-000187.png)

You can also see the run in the Overview section of the Logic App. Click on the last run.

![](/assets/images/2023-10-21-000188.png)

You will find the Temporary Access Pass in the HTTP response.

![](/assets/images/2023-10-21-000189.png)

You can also check the authentication methods blade of your test user.

![](/assets/images/2023-10-21-000190.png)

## What's next?

I hope you've learned a thing or two from this post. This post has shown that automation does not need to be complicated and can be done with low-code solutions like Logic Apps or Power Automate. The combination of Logic Apps, Graph API, and Managed Identities is extremely powerful. This Logic App can be easily extended by other connectors like Microsoft 365.

Stay safe!
