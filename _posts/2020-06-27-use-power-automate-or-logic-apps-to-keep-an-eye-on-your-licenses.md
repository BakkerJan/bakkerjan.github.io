---
title: "Use Power Automate or Logic Apps to keep an eye on your licenses"
date: 2020-06-27
categories: 
  - "entra"
  - "logic-apps"
  - "power-platform"
tags: 
  - "alerts"
  - "azure-ad"
  - "email"
  - "flow"
  - "graph-api"
  - "license"
  - "logic-app"
  - "math"
  - "office365"
  - "power-automate"
  - "power-platform"
  - "teams"
image: "/assests/images/man-in-brown-leather-jacket-using-binoculars-3811807-1.jpg"
---

I guess we've all been there; you ran out of licenses in your Azure AD or Office 365 tenant. Despite you hang out in your admin portal every day, you were still taken by surprise when you discover an issue, caused by a license shortage. More often this is caused by the fact that the people who are responsible to buy these licenses, are not always IT admins. So it's easy to run out of licenses. Time to get this fixed.

In this blog post, I am going to show you how you can keep an eye on your licenses, by alerting when a specific threshold is reached. In this example, I use a threshold of 95%, but depending on your use case, you can change your threshold.

## What do we need for this?

To create these alerts, we use a flow in Power Automate, but you could also use Logic Apps for this. It's up to you. Next, we'll need an app registration in Azure AD with the right permissions to get a list of all the licenses, using Graph API. The HTTP function we use in Power Automate is a premium feature.

I am not going into much detail on how to create an app registration, or how the flow is built op. If you're lost further on in this post, just take a look at my [other post about Power Automate and Graph API](https://janbakker.tech/use-power-automate-for-your-custom-dynamic-groups/).

## App registration

Let's start with creating an app registration in Azure AD. You can find your app registrations under Active Directory -> App registrations in the [Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Give it a proper name, and leave the rest as is.

![](/assets/images/11-27-06-2020.png)

Next, make sure the app registration has the right permissions. Since we are using the [List subscribedSkus](https://docs.microsoft.com/en-us/graph/api/subscribedsku-list?view=graph-rest-1.0&tabs=http) API, you'll need at least **_Organization.Read.All_** application permissions for Graph API. Don't forget to configure the admin consent afterward.

- ![](/assets/images/12-27-06-2020.png)
    
- ![](/assets/images/14-27-06-2020.png)
    

Next, we'll need a client secret for authentication. Create one and copy the secret to notepad. We need this in the next steps. Also, take note of the Application (client) ID en Directory (tenant) ID. So if you followed all the steps, you end up with:

- An app registration with the right Graph API permissions
- A client secret
- The Application (client) ID
- The Directory (tenant) ID

- ![](/assets/images/15-27-06-2020.png)
    
- ![](/assets/images/17-27-06-2020.png)
    

## Create the flow

With all the previous steps in place, we can now go on and create a flow. Head over to you [Power Automate portal](https://flow.microsoft.com/), and start with an empty flow. Pick the scheduled flow to run this daily or weekly, depending on your needs.

![](/assets/images/18-27-06-2020.png)

Our first step is an HTTP request to pull out the license information from our tenant.

![](/assets/images/19-27-06-2020.png)

1. Use the GET method
2. Use the _**https://graph.microsoft.com/v1.0/subscribedSkus**_ API
3. Select Active Directory OAth as your authentication method
4. Enter your tenant ID that you've captured in the previous chapter
5. Use **_https://graph.microsoft.com_** as your audience url
6. Enter your Client ID that you've captured in the previous chapter
7. Enter your secret

This is a good moment to test your flow, to see if you have configured everything correctly. You should end up with the raw JSON data from the Graph API.

![](/assets/images/21-27-06-2020.png)

If everything works as expected, we can now go on and parse our data. Add a new step and select **Parse JSON**. Use the body of the HTTP request and use the output of your HTTP request as sample data to create the schema. Or you can use the sample data from the Microsoft documentation:

```
{
    "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#subscribedSkus",
    "value": [
        {
            "capabilityStatus": "Enabled",
            "consumedUnits": 14,
            "id": "48a80680-7326-48cd-9935-b556b81d3a4e_c7df2760-2c81-4ef7-b578-5b5392b571df",
            "prepaidUnits": {
                "enabled": 25,
                "suspended": 0,
                "warning": 0
            },
            "servicePlans": [
                {
                    "servicePlanId": "8c098270-9dd4-4350-9b30-ba4703f3b36b",
                    "servicePlanName": "ADALLOM_S_O365",
                    "provisioningStatus": "Success",
                    "appliesTo": "User"
                }
            ],
            "skuId": "c7df2760-2c81-4ef7-b578-5b5392b571df",
            "skuPartNumber": "ENTERPRISEPREMIUM",
            "appliesTo": "User"
        },
        {
            "capabilityStatus": "Suspended",
            "consumedUnits": 14,
            "id": "48a80680-7326-48cd-9935-b556b81d3a4e_d17b27af-3f49-4822-99f9-56a661538792",
            "prepaidUnits": {
                "enabled": 0,
                "suspended": 25,
                "warning": 0
            },
            "servicePlans": [
                {
                    "servicePlanId": "f9646fb2-e3b2-4309-95de-dc4833737456",
                    "servicePlanName": "CRMSTANDARD",
                    "provisioningStatus": "Disabled",
                    "appliesTo": "User"
                }
            ],
            "skuId": "d17b27af-3f49-4822-99f9-56a661538792",
            "skuPartNumber": "CRMSTANDARD",
            "appliesTo": "User"
        }
    ]
}
```

Your flow should now look like this. Run a test, to see if your data is parsed correctly.

![](/assets/images/22-27-06-2020.png)

There a couple of values that we need for our alerts:

**_skuPartNumber_**: the name of the SKU  
**_enabled_**: this represents the total amount of installed licenses per SKU  
**_consumedUnits_**: this is the number of licenses in use per SKU

In our next step, we are going to filter the array. I use this step to filter out licenses that have **0** active licenses. This is important because we are going to calculate the percentage later. If you leave them in, the math function, later on, will fail, as it cannot divide by zero. (neither can I ;) )

![](/assets/images/image-89.png)

```
You could also use this step, to filter for specific licenses. In some tenants, there are many different licenses, so you might want to monitor a specific license only.  To to that, just click " Edit in advanced mode" and add the following value: @and(greater(item()?['prepaidUnits']?['enabled'], 1),contains(item()?['skuPartNumber'], 'EMSPREMIUM'))
Replace 'EMSPREMIUM' with the SKU you're interested in.
```

![](/assets/images/image-91.png)

Next, we are going to initialize four variables. First, we create the **threshold** variable to set the alert trigger. Next, we want to work with the **total number** of licenses and the **active number** of licenses, to calculate the **percentage** in use. Create the variables and select **Float** as the data type. In this example, I use 95 (%) as the threshold. You can adjust this to your own needs.

![](/assets/images/36-27-06-2020.png)

![](/assets/images/image-90.png)

In the next step, create an **_Apply to each_** action and set the variables according to the figure below.

```
Make sure you select the body from the filtered array action in the output field, and not the unfiltered parsed data. 
```

In the calculate percentage variable we use an expression in combination with dynamic content. In this expression, I work with the math functions _div_ and _mul_ to calculate the percentage of the licenses in use. In my case, the expression is:

```
mul(div(variables('ActiveLicense'),variables('TotalLicense')),100)
```

This can be a little tricky to set up. Be aware that you use the correct name of the variables. Make sure that you understand what we are doing here: we divide the active licenses by the total licenses and multiply the outcome with 100 to get the percentage. So we are using a 'div' function within a 'mul' function.

You might see this, and think: this can be done in a much easier way! Please, let me know in the comments. I just figured out how to use math functions and dynamic content in a single expression, but I am aware that this might not be the most convenient method ;)

![](/assets/images/38-27-06-2020.png)

The last step is to create a condition with an if-statement. If the threshold is reached, take action. In this case, we send an email to Adele, the license admin of Contoso. If the outcome is false, no further action is taken.

![](/assets/images/30-27-06-2020.png)

The flow will look like this:

![](/assets/images/image-93.png)

## Let's wrap up

Now you can go on and schedule this flow to run whenever you like. I would suggest running this flow daily. Depending on how you set it up, your email will look something like this:

![](/assets/images/34-27-06-2020.png)

You can also create other actions, like messages on Teams, service tickets, or text messages for example.

#### Variations

In this example, I use a percentage to be my threshold, but you can be creative, and adjust this to your needs. You might have just one or two licenses, and want to get alerted when you've got 10 licenses left. In that case, you adjust the threshold to 10 and then use the expression with the math function to calculate the free licenses:

```
sub(variables('TotalLicense'),variables('ActiveLicense'))
```

![](/assets/images/image-94.png)

You can now use this outcome in your if-statement to trigger the alert when the licenses are less than your threshold.

![](/assets/images/image-95.png)

Be creative!

## Some useful reference

[Reference guide to using functions in expressions for Azure Logic Apps and Power Automate](https://docs.microsoft.com/en-us/azure/logic-apps/workflow-definition-language-functions-reference)

[Power Automate documentation](https://docs.microsoft.com/en-us/power-automate/)

[Microsoft Graph REST API v1.0 reference](https://docs.microsoft.com/en-us/graph/api/overview?view=graph-rest-1.0)
