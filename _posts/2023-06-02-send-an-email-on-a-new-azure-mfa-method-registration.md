---
title: "Send an email on a new Azure MFA method registration"
date: 2023-06-02
categories: 
  - "entra"
  - "power-platform"
  - "security"
coverImage: "pexels-ivan-samkov-4240545-scaled.jpg"
---

I've done quite some Azure MFA projects over time (and counting), and as we mainly focus on the technical side, there are also practical sides to consider. Every project has its own approach and challenges, and more importantly: the user is impacted more or less, and that asks for some guidance.

Now, this solution comes in handy if you want to act on new registrations for Azure MFA methods. That can be an action of any kind. To give you some examples:

- send an email

- send a Teams chat

- add user to a group

- add details to a reporting database

I can think of many ways of doing this, like using Log Analytics and Azure Monitor, but for now, we'll stick with Power Automate, SharePoint List, and Graph API. I want to keep things simple.

The solution looks like this:

![](/assets/images/Report-Azure-MFA.png)

Flow 1 is polling every x minutes and grabs the audit logs in Azure AD, looking for a specific event. In this case, we want to search for users that have registered all Azure AD methods successfully. As a result, the users in scope are added to a SharePoint List, waiting for flow 2 to kick in:

![](/assets/images/Send-email-to-new-registrations.png)

Flow 2 will trigger on any new item that is added to a SharePoint List. It uses the Office 365 connector to grab the email address for the user, and then sends an email.

## Let's build this

In order to build this, we need a few things:

- An Azure AD app registration with application permissions **AuditLog.Read.All**

- A secret, tenant ID, and client ID of the app registration

- A SharePoint List with two columns: **_userPrincipalName_** and **_CommSent_**. The latter one will be updated when communication is successfully sent to keep track of things.

- A premium license (or trial) for Power Automate

Let's start with the HTTP action to pull the data from the Audit Logs.

![](/assets/images/image-6.png)

For the URI, use:

```
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits
```

Then, add the $filter query, and add this as the value:

```
activityDisplayName eq 'User registered all required security info' and Activitydatetime ge @{body('Get_past_time')} and result eq 'success'
```

I use the '_Get past time_' action to define the scope of the query. In this case, only results from the last 15 minutes will be shown.

Use the tenantID, ClientID, and secret from the newly created app registration for authentication. If this is new to you, please check [this post](https://janbakker.tech/use-power-automate-for-your-custom-dynamic-groups/) for a more detailed example.

It's a good practice first to try the query using [Graph Explorer](https://aka.ms/ge) to see if you receive the right results. The complete URI will look like this:

```
https://graph.microsoft.com/v1.0/auditLogs/directoryAudits?$filter=activityDisplayName eq 'User registered all required security info' and Activitydatetime ge 2023-06-02T08:02:17.0339608Z and result eq 'success'
```

![](/assets/images/image-9.png)

We will need the output of this step to create the schema for the Parse JSON action. In my case, the schema looked like this:

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
                    "category": {
                        "type": "string"
                    },
                    "correlationId": {
                        "type": "string"
                    },
                    "result": {
                        "type": "string"
                    },
                    "resultReason": {
                        "type": "string"
                    },
                    "activityDisplayName": {
                        "type": "string"
                    },
                    "activityDateTime": {
                        "type": "string"
                    },
                    "loggedByService": {
                        "type": "string"
                    },
                    "operationType": {
                        "type": "string"
                    },
                    "initiatedBy": {
                        "type": "object",
                        "properties": {
                            "app": {},
                            "user": {
                                "type": "object",
                                "properties": {
                                    "id": {
                                        "type": "string"
                                    },
                                    "displayName": {},
                                    "userPrincipalName": {
                                        "type": "string"
                                    },
                                    "ipAddress": {
                                        "type": "string"
                                    },
                                    "userType": {},
                                    "homeTenantId": {},
                                    "homeTenantName": {}
                                }
                            }
                        }
                    },
                    "targetResources": {
                        "type": "array",
                        "items": {
                            "type": "object",
                            "properties": {
                                "id": {
                                    "type": "string"
                                },
                                "displayName": {
                                    "type": "string"
                                },
                                "type": {
                                    "type": "string"
                                },
                                "userPrincipalName": {
                                    "type": "string"
                                },
                                "groupType": {},
                                "modifiedProperties": {
                                    "type": "array"
                                }
                            },
                            "required": [
                                "id",
                                "displayName",
                                "type",
                                "userPrincipalName",
                                "groupType",
                                "modifiedProperties"
                            ]
                        }
                    },
                    "additionalDetails": {
                        "type": "array"
                    }
                },
                "required": [
                    "id",
                    "category",
                    "correlationId",
                    "result",
                    "resultReason",
                    "activityDisplayName",
                    "activityDateTime",
                    "loggedByService",
                    "operationType",
                    "initiatedBy",
                    "targetResources",
                    "additionalDetails"
                ]
            }
        }
    }
}
```

Use this data to make the next step in the flow. We parse the JSON data, so we can use it in our next steps.

![](/assets/images/image-2.png)

Next, we want to check if the user already exists on the SharePoint list. We don't want double entries.

![](/assets/images/image-7.png)

For the Filter Query, use this value:

```
userPrincipalName eq '@{items('Apply_to_each')?['initiatedBy']?['user']?['userPrincipalName']}'
```

If the user does not exist, a new item is created on the SharePoint List.

![](/assets/images/image-8.png)

To check if the user exists on the list, we can simply use a condition:

```
length(outputs('Get_items')?['body/value'])
```

If the output is empty, we can assume the user is not listed yet. If we now run the flow, we can see that Chandler Bing is added, since he registered his security information a few minutes ago.

![](/assets/images/image-10.png)

We can see Chandler is now sitting as an item on our SharePoint List̀

![](/assets/images/image-11.png)

## Flow 2 - Send an email

With the first (and hardest) part in place, we can now build the second flow. This flow will trigger on any new item that is added to our SharePoint list. We will check if the **CommSent** value is set to **No**, to avoid spamming.

![](/assets/images/image-13.png)

The next steps are pretty straightforward.

![](/assets/images/image-14.png)

Using the dynamic content, we pull the users' info like Email and Given Name from the Office 365 connector. Next, we'll send an email and update the item on the SharePoint list.

![](/assets/images/image-15.png)

## Let's wrap up

The beauty of this post (if you ask me) is the concept. This usecase was about Azure MFA, but imagine the amount of valuable data that is stored in the Azure AD audit logs. You can take this concept and use it for many other things during your projects.  
Just edit the query parameters to Graph API, and off you go.

I hope this post was valuable to you.

Stay safe!
