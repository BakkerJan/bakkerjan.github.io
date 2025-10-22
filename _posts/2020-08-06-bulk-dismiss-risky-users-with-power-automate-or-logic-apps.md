---
title: "Bulk dismiss risky users with Power Automate or Logic Apps"
date: 2020-08-06
categories: 
  - "entra"
  - "logic-apps"
  - "power-platform"
  - "security"
tags: 
  - "azure-ad-identity-protection"
  - "custom-connector"
  - "dismiss"
  - "graph-api"
  - "logic-app"
  - "power-automate"
  - "risky-users"
image: "/assets/images/iStock-1155623694.jpg"
---

```
Update 08-10-2020: Microsoft released an official connector for Azure AD Identity Protection. This would be much easier to use, since you don't have to create a service principal to authenticate the custom connector. However, at the time of writing the official connector does not have the action to get all the risky users. Will keep an eye on things. 
```

This blog was inspired by an [Azure AD Mailbag blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-mailbag-identity-protection/ba-p/1257350) about Azure AD Identity Protection. In the article, Microsoft provided a [PowerShell script sample](https://github.com/AzureAD/IdentityProtectionTools) that you can use for bulk dismissal of risky users. Today I will show you how you can use either Power Automate or Logic Apps to do the same. You could use the Graph API with an HTTP request for this, but to make things simple, I created a custom connector. I used the [riksyUser Graph API](https://docs.microsoft.com/en-us/graph/api/resources/riskyuser?view=graph-rest-beta).

![](/assets/images/iStock-1155623694.jpg)

* * *

If you have never worked with app registrations and custom connectors, I suggest you [check out this blog](https://janbakker.tech/prepopulate-phone-methods-using-a-custom-connector-in-power-automate/) where I worked out some more detailed steps on every subject. You can use it as a reference guide.

## What do we need?

First, we need the custom connector source files to install the connector in your own tenant. You can [grab them from Github](https://github.com/BakkerJan/Power-Automate/tree/master/Azure%20AD%20risky%20users%20-%20Custom%20Connector).

Next, we need an app registration for authentication. Create a new app registration with the following properties:

Permissions: **_IdentityRiskyUser.ReadWrite.All_** Graph API application permissions.  
Web redirect URL: **_https://global.consent.azure-apim.net/redirect_**

Create a new **_secret_** and store it in Notepad together with the **_Application(client) ID_** and **_Directory (tenant) ID_**.

![](/assets/images/image-6.png)

## Install the custom connector

Upload the custom connector by using the browser or the [paconn CLI tool](https://github.com/microsoft/PowerPlatformConnectors/tree/master/tools/paconn-cli). Enter the OAth2.0 credentials and make sure that the connector works correctly by testing the ListRiskyUsers action for example.

![](/assets/images/image.png)

## Build the flow

Now that we have installed the Risky Users connector, we can build our flow. Start off with a blank automated flow and use manual flow trigger.

Next, add the custom connector action ListRiskyUsers and select the preferred filter.

![](/assets/images/552-06-08-2020.png)

You can add a custom value if you want to be more specific.

In the next step, I make sure that the risk of the users was updated in the past 30 days, just like the sample script does. Create a Filter array action, and add the custom expression:

```
addDays(utcnow('yyyy-MM-ddTHH:mm:ssZ'), -30)
```

Use the output (value) from the first action as your input. You can select the riskLastUpdate parameter from the previous step.

![](/assets/images/image-2.png)

Add the last two steps to complete te flow. First, we have to lookup the ID of the user and than dismiss the risk.

![](/assets/images/image-3.png)

Make sure that you selected the output of the filter array (30 days) as your input. From the Azure AD connector, select the **Get User** action and provide the UserPrincipalName from the previous step.

Next, add the **Dismiss Risky user** action from the custom connector, and add the **Id** from the user.

In my demo tenant, I got only 4 risky users. Test and run the flow.

![](/assets/images/image-4.png)

The API returns an empty **_204_** response, but you can check the user object to see of the risk is dismissed.

![](/assets/images/image-5-1024x540.png)

## Wrap up

I hope this blog post was helpful. This is just another good example on how easy you can automate with the use of Graph API and Power Automate or Logic Apps.

If you want to get started with building your own custom connector, please check [this blog post](https://powerusers.microsoft.com/t5/Power-Automate-Community-Blog/Build-a-custom-connector-for-Microsoft-Graph-API/ba-p/647492) I wrote on the Power Platform community website.

Stay safe!
