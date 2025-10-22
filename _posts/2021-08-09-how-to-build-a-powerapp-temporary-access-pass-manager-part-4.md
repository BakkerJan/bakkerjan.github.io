---
title: "How to build a PowerApp - Temporary Access Pass Manager – Part 4"
date: 2021-08-09
categories: 
  - "entra"
  - "power-platform"
  - "security"
image: "/assests/images/1628535438.png"
---

## Part 4: Build a custom connector based on the Graph API

Now that we know the basics, as discussed in previous parts, it's time to build the custom connector. This custom connector is going to use the Graph API. Before we start building, make sure you have these things at hand:

1. The appliation ID and secret of the app registration (part 2)
2. The Graph Explorer, and the sample queries (part 3)
3. A test user in Azure Active Directory
4. A license to user PowerApps/Power Automate. Click [here](https://go.microsoft.com/fwlink/?LinkId=2104321&cmpid=pap-pric-body-try-cardperuser&clcid=0x409) to start a trial if you don't have a license

**Important!**

Both the name of the connector and the actions (operation ID) are going to be used in the PowerApp later on. It's recommended to use the same name convention as the example, so you don't have to change all the queries manually later.

| Item | Name operation ID |
| --- | --- |
| Name of the custom connector | **TemporaryAccessPass** |
| Action to create Temporary Access Pass | **CreateTemporaryAccessPass** |
| Action to list Temporary Access Pass | **ListTemporaryAccessPass** |
| Action to get Temporary Access Pass | **GetTemporaryAccessPass** |
| Action to delete Temporary Access Pass | **DeleteTemporaryAccessPass** |

## Custom connector

Go to [https://make.powerapps.com](https://make.powerapps.com) and under the data section, go to customer connectors. Click **_\+ New custom connector_** to get started. Select **_Create from blank_** to build the connector from scratch.

![](/assets/images/image-30.png)

Let's give the connector a proper name. Be aware that you need the name of the connector in the next part of the series when we are using it with PowerApps. In my example, I use **TemporaryAccessPass** as the name of the connector.

![](/assets/images/image-31.png)

Enter the details in the first screen of the wizard.

1. Upload a logo if you like. Download the one from the example from [here](https://github.com/BakkerJan/PowerApps/blob/main/Custom%20Connector/LogoMakr-8oR8m9.png).
2. As background color I use white (#ffffff) You can pick any color you like.
3. Enter a brief description for your connector
4. Use HTTPS (default)
5. Enter **_graph.microsoft.com_** as your host URL

Next, click **_Security ->_** to move to the next screen.

In the next screen, fill out the security information in order to authenticate to Azure AD. Here is where the app registration is making it's entrance.

![](/assets/images/image-32.png)

1. Choose **_OAuth 2.0_**
2. Select **_Azure Active Directory_**
3. Enter the _**client ID / Application ID**_ of the app registration (captured in part 2)
4. Enter the **_secret_** that we created earlier (captured in part 2) If you forgot to save this secret, create a new one, and delete the old one.
5. Use **_https://graph.microsoft.com_** as you resource URL.

After this, click **_Create connector_**! This will save the configuration so far. After that, move to the next screen: Definition.

## Definition

In this screen, we are going to define four actions: create, list, get, and delete Temporary Access Pass. Let's start with the first one. Fill in the summary, description, and operation ID. For both summary and operation ID, I use **CreateTemporaryAccessPass**. Description can be anything.

After that, click **\+ Import from sample**.

![](/assets/images/1628431217.png)

Choose **POST** for the Verb, and use this URL\*:

```
https://graph.microsoft.com/beta/users/{UPN}/authentication/temporaryAccessPassMethods
```

\*Here we are creating a _variable_ for the UserPrincipalName by using {UPN} in the URL. Use this exact URL; you don't have to replace the UPN with your test users' UPN here.

Enter this value in the body, and click **Import**.

```
{"JSON":""}
```

Now, we need to configure some additional steps for the body parameter. These are important!

<figure>

![](/assets/images/image-55.png)

<figcaption>

Edit the body parameter

</figcaption>

</figure>

<figure>

![](/assets/images/image-56.png)

<figcaption>

Set 'Is required' to Yes and edit the JSON body

</figcaption>

</figure>

<figure>

![](/assets/images/image-57.png)

<figcaption>

Add the two brackets as the default value and set 'Is required' to Yes

</figcaption>

</figure>

After changing the JSON/body parameters, click **_<-- back_** twice to return to the 'home screen.  
After that, click **\+ Add default response**, and use this as the body:

```
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#users('AdeleV%40M365x583104.OnMicrosoft.com')/authentication/temporaryAccessPassMethods/$entity",
    "id": "42cf61da-0b54-4b61-b6cd-362343f41a0e",
    "temporaryAccessPass": "Q$wSrZ3R",
    "createdDateTime": "2021-08-07T13:02:07.5740124Z",
    "startDateTime": "2021-08-07T13:02:06.2614145Z",
    "lifetimeInMinutes": 60,
    "isUsableOnce": false,
    "isUsable": true,
    "methodUsabilityReason": "EnabledByPolicy"
}
```

![](/assets/images/image-35.png)

To create the response sample yourself, simply use the Graph Explorer to create the queries and copy the response. You can also use the sample response from the documentation for this.

When you click on the default response after the import, you should see the payload. This will represent the schema for the response. Should look like this:

![](/assets/images/image-36.png)

Update the connector and move to the last screen in the wizard: **Test**

## Test

Before we can test the connector, we need to create a new connection. Click **_\+ New connection_**, and select the account that you want to use. Remember that this account needs one of these roles to create a Temporary Access Pass:

- Global admin
- Privileged authentication admin
- Authentication admin

![](/assets/images/image-38.png)

Next, enter the UPN of the test user and test the operation. The body can be empty, but two brackets will also work. You should see a valid response and a **201** response code. Make sure that your test user has a Temporary Access Pass yet. Otherwise, this test will fail.

![](/assets/images/image-37.png)

If your test was successful, we can now go on to create the other actions.

## Get, List and Delete

Now, repeat the above steps to create the remaining three definitions.

## ListTemporaryAccessPass

![](/assets/images/image-39.png)

**Summary**: ListTemporaryAccessPass  
**Description**: List Temporary Access Pass  
**Operation ID**: ListTemporaryAccessPass  
**Verb**: GET  
**URL**: https://graph.microsoft.com/beta/users/{UPN}/authentication/temporaryAccessPassMethods  
Response sample:

```
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#users('AdeleV%40M365x583104.OnMicrosoft.com')/authentication/temporaryAccessPassMethods",
    "value": [
        {
            "id": "6971aea8-147e-478f-809b-6fa29d42edfa",
            "temporaryAccessPass": null,
            "createdDateTime": "2021-08-07T13:23:32.6999338Z",
            "startDateTime": "2021-08-07T13:23:31.2392129Z",
            "lifetimeInMinutes": 60,
            "isUsableOnce": false,
            "isUsable": true,
            "methodUsabilityReason": "EnabledByPolicy"
        }
    ]
}
```

## GetTemporaryAccessPass

![](/assets/images/image-40.png)

**Summary**: GetTemporaryAccessPass  
**Description**: Get Temporary Access Pass  
**Operation ID**: GetTemporaryAccessPass  
**Verb**: GET  
**URL**: https://graph.microsoft.com/beta/users/{UPN}/authentication/temporaryAccessPassMethods/{id}  
Response sample:

```
{
  "@odata.context": "https://graph.microsoft.com/beta/$metadata#users('AdeleV%40M365x583104.OnMicrosoft.com')/authentication/temporaryAccessPassMethods/$entity",
  "id": "2cbf352d-3c26-4d74-a48d-692d8f7cdb3a",
  "temporaryAccessPass": null,
  "createdDateTime": "2021-08-08T17:41:32.147959Z",
  "startDateTime": "2021-08-08T17:41:31.6435277Z",
  "lifetimeInMinutes": 60,
  "isUsableOnce": false,
  "isUsable": true,
  "methodUsabilityReason": "EnabledByPolicy"
}
```

## DeleteTemporaryAccessPass

![](/assets/images/image-41.png)

**Summary**: DeleteTemporaryAccessPass  
**Description**: Delete Temporary Access Pass  
**Operation ID**: DeleteTemporaryAccessPass  
**Verb**: DELETE  
**URL**: https://graph.microsoft.com/beta/users/{UPN}/authentication/temporaryAccessPassMethods/{id}  
Response sample: none \*

\*If successful, this method returns a `204 No Content` response code. It does not return anything in the response body.

Don't forget to update your connector to save the changes!

If all actions and responses are added, you should be able to successfully test all four actions. I suggest testing in this order:

1. Create Temporary Access Pass
2. List Temporary Acces Pass (grab the ID to test the next actions)
3. Get Temporary Access Pass (using ID from previous step)
4. Delete Temporary Access Pass (using ID from previous step)

![](/assets/images/image-42.png)

## HTTP headers

One last (important) thing before the custom connector is ready for use: **_headers_**. Most of the POST requests to Graph API require headers. You can read this in the documentation.

![](/assets/images/image-73.png)

So, we are going to use a policy to fix that. I did not work with policies before, so this is a nice way to introduce them. In the Definition tab, click **\+ New policy**.

![](/assets/images/image-74.png)

1. Provide a name for the policy, in this case 'Set HTTP header'
2. Choose the template "Set HTTP header"
3. Select **_only_** the CreateTemporaryAccessPass operation. The other operation don't require a header.
4. Enter: 'content-type'
5. Enter: 'application/json'
6. Choose for 'override'. Any other header will be replaced.
7. Select 'Request'. The policy will only applu on the request.

Don't forget to update your connector to save the changes!

Okay, you've come really far! Now it's time to use our connector in Power Apps and Power Automate and build a nice app around it! Click here if you are ready for the final step. It's going to be fun! [Part 5: Create an app in PowerApps using a custom connector](https://janbakker.tech/how-to-build-a-powerapp-temporary-access-pass-manager-part-5/)
