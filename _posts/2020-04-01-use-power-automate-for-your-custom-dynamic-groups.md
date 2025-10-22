---
title: "Use Power Automate for your custom \"dynamic\" groups"
date: 2020-04-01
categories: 
  - "power-platform"
tags: 
  - "automate"
  - "azure-ad"
  - "dynamic"
  - "flow"
  - "graph-api"
  - "groups"
  - "http"
  - "json"
  - "mfa"
  - "power-automate"
  - "power-platform"
  - "registered"
image: "/assests/images/RPA.51e0cbd49adc25e8c3328025126987f0.2.png"
---

## Azure AD Dynamic Groups

Dynamic groups in Azure AD are awesome. I use them a lot. Dynamic groups can create groups based on attributes. For example, you can create a group that includes all the users from the Sales Team. The query for the group would look like this:

![Azure AD dynamic groups](/assets/images/msedge_pLdYeFKy43.png)

If a new user comes along with the same attribute, the user will automatically be added to this group. This can be really helpful for onboarding.

You can pick a lot of attributes, and you can create rules with multiple expressions or even custom extension properties are supported. Check the [Microsoft docs](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/groups-dynamic-membership) that you can do with Azure AD Dynamic groups.

## So, what's the problem here?

But what if you want to create a dynamic group, based on an attribute that's not that easy to find? I stumbled upon [this Uservoice](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/36980380-dynamic-groups-based-on-strongauthenticationmethod) and that got me thinking. Can I create a group based on the StrongAuthenticationMethods values? At the time of writing, these values are only accessible through PowerShell or Graph API.

In this blog post, we are going to use Graph API to collect these values and we use Power Automate to create the Azure AD group. What we'll end up with is a "dynamic" group, containing all the **MFA registered users** in your Azure AD tenant, both synced and cloud-only accounts. Depending on your recurring value, the group is updated periodically.

```
I can imagine you are looking for the opposite: a group with all the users that did not register for MFA. I'll get to that later.
```

I use the **credentialUserRegistrationDetails** API. This will give me the following values:

```
{
      "id" : "id-value",
      "userPrincipalName":"userPrincipalName",
      "userDisplayName": "userDisplayName-value",
      "authMethods": ["email", "mobileSMS"],
      "isRegistered" : false,
      "isEnabled" : true,
      "isCapable" : false,
      "isMfaRegistered" : true
    }
```

## Let's dive right in

So, what do you need?

- Power Automate subscription with **_premium_** connectors (or trial)
- Enough rights to create an app registration in Azure AD (+admin consent), create a security group and add members to it.
- A bunch of users registered for Azure MFA

#### Create the app registration

In order to use the Graph API from Power Automate, we need proper rights. Therefore we create an app registration in Azure AD and give it the right permissions.

Go to Azure Active Directory -> App registrations and click the **\+ New registration** button.

![New app registration](/assets/images/image-36.png)

Next, give it a proper name, and fill the URL with the value **https://auth**

![Register an application](/assets/images/image-37.png)

Once created, go to the API Permissions tab.

![Configure permissions](/assets/images/image-38.png)

Add the following permissions for the **Microsoft Graph**:

| Permission type | Permissions |
| --- | --- |
| Delegated | Reports.Read.All |
| Application | Reports.Read.All |
| _Delegated_ | _Audit.Read.All_ \* |
| _Application_ | _Audit.Read.All_ \* |

\*_At the time of writing, there is [a bug](https://github.com/microsoftgraph/microsoft-graph-docs/issues/5598) in this API. You should temporarily add the Audit.Read.All permission (as well), otherwise you will not get the right permissions to call this API._ You can check the [current documentation](https://docs.microsoft.com/en-us/graph/api/reportroot-list-credentialuserregistrationdetails?view=graph-rest-beta&tabs=http) on this for further information. If you just follow the documentation, you will end up with the following error:

![Error API bug](/assets/images/image-1.png)

When you added the permissions, grant **admin consent**

![Admin consent](/assets/images/image-2.png)

The last thing we need is a client secret. Go to the Certificates & secrets tab, create a new client secret. _**Copy the secret value, you'll need it later on.**_ Also, take note of the **Tenant & Client ID** on the Overview page.

![Create the secret](/assets/images/image-40.png)

## Create the group

Now that we have created the app registration in order to pull the data out of the Graph API, we need an Azure AD Group to put our users in.

Created an **Assigned** Securitygroup in Azure AD and leave it as is. For later use, take note of the Object ID of the group.

- ![](/assets/images/image.png)
    
- ![](/assets/images/msedge_8awBaWmNlL-1024x587.png)
    

## Go with the Flow

Now our preparations are done, we can continue to Power Automate to create the flow. Head over to [https://flow.microsoft.com](https://flow.microsoft.com) to create a new Flow. Choose for a scheduled flow, start from blank. Choose the name and schedule. I suggest running this every 8 or 12 hours.

![Build a scheduled flow](/assets/images/image-41.png)

We start by adding a new step to the flow. Search for **HTTP**. This is a premium connector, If you don't have a proper license, you can start a trial right away. This step will pull the data from the [Graph API](https://docs.microsoft.com/en-us/graph/api/reportroot-list-credentialuserregistrationdetails?view=graph-rest-beta&tabs=http). The filter is to just get the users that are registered for MFA.

![HTTP connector](/assets/images/image-42.png)

**Method:** GET

**URI:** https://graph.microsoft.com/beta/reports/credentialUserRegistrationDetails

**Queries**: $filter | isMfaRegistered eq true

**Authentication**: Active Directory OAuth

**Tenant & Client ID**: Pick the tenant and client ID from the overview page of your app registration you've created earlier

**Audience:** https://graph.microsoft.com

**Secret**: Paste the secret that you created.

```
If you are looking for a group with all the users that did not register for MFA, just change the value isMfaRegistered eq true to isMfaRegistered eq false
All the other steps remain the same. You only might want to change the displayname of the group. 
```

#### Paging and Asynchronous Pattern

Make sure you enable [paging](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-exceed-default-page-size-with-pagination) on your HTTP request when you handle a lot of objects. Make sure **Asynchronous Pattern** is enabled.

![](/assets/images/msedge_hrwpkrN2vt.png)

Next, we need to parse the data that we received from the Graph so that we can grab the values that we need for the next steps. So click + **Add New step** and look for the "parse JSON" action. In the content field, select the **Body** from the HTTP request.

![Parse JSON](/assets/images/image-43.png)

Copy and paste this section into the schema section:

```
{
    "type": "object",
    "properties": {
        "@@odata.context": {
            "type": "string"
        },
        "value": {
            "type": "array",
            "items": {
                "type": "object",
                "properties": {
                    "id": {
                        "type": "string"
                    },
                    "userPrincipalName": {
                        "type": "string"
                    },
                    "userDisplayName": {
                        "type": "string"
                    },
                    "isRegistered": {
                        "type": "boolean"
                    },
                    "isEnabled": {
                        "type": "boolean"
                    },
                    "isCapable": {
                        "type": "boolean"
                    },
                    "isMfaRegistered": {
                        "type": "boolean"
                    },
                    "authMethods": {
                        "type": "array",
                        "items": {
                            "type": "string"
                        }
                    }
                },
                "required": [
                    "id",
                    "userPrincipalName",
                    "userDisplayName",
                    "isRegistered",
                    "isEnabled",
                    "isCapable",
                    "isMfaRegistered",
                    "authMethods"
                ]
            }
        }
    }
}
```

**_(TIP)_** In order to get the right schema, just run/test the flow and copy the HTTP output. Instead of directly pasting the schema, just click **Generate from sample** and paste the output data from either your HTTP connector or the Graph Explorer. The schema will be generated, based on the sample data.

- ![](/assets/images/msedge_ljiiZyDbuU-1024x501.png)
    
- ![](/assets/images/image-4-1024x593.png)
    

#### _Optional step: filter array_

Sometimes the outcome of the JSON has empty (null) values. This will result in this error:

```
The execution of template action ‘Apply_to_each’ failed: the result of the evaluation of ‘foreach’ expression ‘@body (‘ Parse_JSON ‘)? [‘ properties’]? [ ‘value’] ‘is of type’ Null ‘. The result must be a valid array.
```

You can add an aditional step to filter out the null values.

![](/assets/images/image-15.png)

In our next step, we are going to add the Azure AD group to the flow. To do that, click + New Step and choose the Get group action for the Azure AD Connector. If this is the first time you use it, you'll need to login in first.

Next, copy the Object ID from the Azure AD group you created earlier and past this in the group Id field in the flow.

- ![](/assets/images/msedge_VP7rA2o295-1024x749.png)
    
- ![](/assets/images/image-5.png)
    

So, now we created the base for our flow. We've captured the users, and specified the group. Next up, we are going to add each user to the group. In order to do that, we use an Apply to Each action and use the value form the parse JSON action.

![Apply to each](/assets/images/msedge_RdyTu3LUe7.png)

Within the Appy to each section, click **Add an action**, and look for the Get User action (Azure AD). Here, we use the UserPrincipalName to get the ID for the users. This is needed to add the user to the group later.

![Apply to each](/assets/images/image-6.png)

Next, we are going to check of the user is already a member of the group. Add another action and look for **Check User Membership V2**. Select the **User** ID from the Dynamic content panel for the top field and the **Group** ID for the second field.

- ![](/assets/images/msedge_7wt6vvbBHc-1024x771.png)
    
- ![](/assets/images/msedge_GtbAavSide-1024x805.png)
    

Now, we are adding a Condition Control. The outcome will be Yes or No. Here we check if the user is a member of the group. So if the value contains the group ID, the user is already a member of the group. Else, it's No, so we need to add the user in the next step.

Make sure you use the **contains** statement.

- ![](/assets/images/msedge_hxQCb4k4M4-1024x587.png)
    
- ![](/assets/images/msedge_h8rO4W2e9j-1024x493.png)
    

The last step in the flow is to add the user to the group. Add a new action in the "If No" section and look for **Add user to group.** Next, pick the right values from the dynamic content panel.

- ![](/assets/images/msedge_NUYG2sbCNm-1024x703.png)
    
- ![](/assets/images/msedge_5WokRb9Mde-1024x747.png)
    

The "If Yes" section can stay empty. Next, save the flow. The flow should look like this:

![Overview of the flow](/assets/images/image-9.png)

## Test the flow

To test the flow, click the Test button in the right upper corner.

- ![](/assets/images/msedge_wLMkhmD9sQ-1024x303.png)
    
- ![](/assets/images/msedge_mLVRVMcUxP-1024x377.png)
    
- ![](/assets/images/msedge_r4FG416sgA-1024x360.png)
    

When the flow fails, you can check the errors to resolve them. If successful, you can see the output and go through the data.

![Flow succesful](/assets/images/image-7.png)

In Aure AD you can check if the members are added to the group.

![Audit logs](/assets/images/image-8.png)

## Improvements

If you have a lot of users, you can enable Concurrency Control on the Apply to each action. You can adjust the value to your needs. You can lower the value if your flow keeps failing, or taking a long time to succeed.

- ![](/assets/images/msedge_FORMQJEbwL-1024x402.png)
    
- ![](/assets/images/image-10-1024x526.png)
    

## Wrap things up

Be aware that I used the **BETA** Graph API here, so you should be careful using this in production environments. Also, this gives you an idea of what you can do with Graph API and Power Automate. This was my first experience with Power Automate, so I guess there are some improvements to make here. Just let me know what I can do better, and I'll update the article.
