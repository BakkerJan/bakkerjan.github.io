---
title: "Use Power Automate as your Conditional Access Police Department"
date: 2020-07-04
categories: 
  - "entra"
  - "logic-apps"
  - "power-platform"
  - "security"
tags: 
  - "administrators"
  - "break-glass"
  - "conditional-access"
  - "flow"
  - "power-automate"
image: "/assests/images/police-fun-funny-uniform-33598-scaled.jpg"
---

Last week, I was working on a [new blog for the Secure Score Series](https://janbakker.tech/microsoft-secure-score-series-14-designate-more-than-one-global-admin/) regarding global admin and break glass accounts. I came to the point where I was thinking of possible scenarios that could go wrong with these accounts. What if someone accidentally added these users to a certain group? What if that group would be triggered in some policy or maintenance tasks? A lot of these actions can be discovered using Microsoft Cloud App Security and Azure Monitor. This way, you will be alerted when someone touches the accounts in any way, or if the account is used to sign-in.

![](/assets/images/man-working-using-a-laptop-2696299-scaled.jpg)

But then I thought of Conditional Access policies. There are some best practices regarding designing these policies. One of them is to exclude your service accounts, and/or your break glass admin accounts from all of your policies. But what if you forget this? Or what if someone modifies the policy afterward? Then I came up with the idea for this blog.

## What are we building?

This solution is not bulletproof, but you can see this as an extra layer of control. Not all companies have automated their configuration of Conditional Access. So, policies are created manually. And where people work, mistakes are made. So imagine you create a brand new policy and you forget to exclude the standard exclusion groups? So, in case of an emergency, when the break glass account is used, you don't want to be taken by surprise.

So I build a flow in Power Automate that checks the Conditional Access policies on by one and looks at the excluded groups. When the specific group is not present, an alert is sent out to the administrators. Take note that you could also use Logic Apps for this. Let's get started!

## First things first

Let's begin with registering an app registration in Azure AD. If you don't know how to to that, take a look at [this blog](https://janbakker.tech/use-power-automate-or-logic-apps-to-keep-an-eye-on-your-licenses/) first. Next, add the right permissions to the app registration and create a secret.

![](/assets/images/image-6.png)

Before we can build the next step, make sure you have the following info at hand:

- A client secret
- The Application (client) ID of the app registration
- The Directory (tenant) ID

## Build the flow

Head over to [https://flow.microsoft.com/](https://flow.microsoft.com/) and create a new recurring flow. The flow itself is pretty easy. First, we start with an HTTP request to pull out all the Conditional Access policies. We use the [Get conditionalAccessPolicy](https://docs.microsoft.com/en-us/graph/api/conditionalaccesspolicy-get?view=graph-rest-beta&tabs=http) API for this. In this example, I added a filter to just select the enabled policies.

![](/assets/images/image-7.png)

1. Use the **GET** method
2. Use the _**https://graph.microsoft.com/beta/identity/conditionalAccess/policies**_ API
3. Use a filter query **_state eq 'enabled'_** to just pull out the enabled policies. Take note that you can also use **'disabled'** and **'enabledForReportingButNotEnforced'** in this query.
4. Select Active Directory OAth as your authentication method
5. Enter your tenant ID that you’ve captured in the previous chapter
6. Use **_https://graph.microsoft.com_** as your audience url
7. Enter your Client ID that you’ve captured in the previous chapter
8. Enter your secret

Next, we have to parse the JSON. Now, this can be tricky. The schema is changing, depending on how your policy is built.

![](/assets/images/image-8.png)

You can take the output of the HTTP request as an example to create the schema. I use the following schema:

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
                    "displayName": {
                        "type": "string"
                    },
                    "createdDateTime": {},
                    "modifiedDateTime": {},
                    "state": {
                        "type": "string"
                    },
                    "sessionControls": {},
                    "conditions": {
                        "type": "object",
                        "properties": {
                            "signInRiskLevels": {
                                "type": "array"
                            },
                            "clientAppTypes": {
                                "type": "array",
                                "items": {
                                    "type": "string"
                                }
                            },
                            "platforms": {},
                            "locations": {},
                            "deviceStates": {},
                            "devices": {},
                            "applications": {
                                "type": "object",
                                "properties": {
                                    "includeApplications": {
                                        "type": "array",
                                        "items": {
                                            "type": "string"
                                        }
                                    },
                                    "excludeApplications": {
                                        "type": "array"
                                    },
                                    "includeUserActions": {
                                        "type": "array"
                                    }
                                }
                            },
                            "users": {
                                "type": "object",
                                "properties": {
                                    "includeUsers": {
                                        "type": "array",
                                        "items": {
                                            "type": "string"
                                        }
                                    },
                                    "excludeUsers": {
                                        "type": "array"
                                    },
                                    "includeGroups": {
                                        "type": "array"
                                    },
                                    "excludeGroups": {
                                        "type": "array"
                                    },
                                    "includeRoles": {
                                        "type": "array"
                                    },
                                    "excludeRoles": {
                                        "type": "array"
                                    }
                                }
                            }
                        }
                    },
                    "grantControls": {
                        "type": "object",
                        "properties": {
                            "operator": {
                                "type": "string"
                            },
                            "builtInControls": {
                                "type": "array",
                                "items": {
                                    "type": "string"
                                }
                            },
                            "customAuthenticationFactors": {
                                "type": "array"
                            },
                            "termsOfUse": {
                                "type": "array"
                            }
                        }
                    }
                },
                "required": [
                    "id",
                    "displayName",
                    "createdDateTime",
                    "modifiedDateTime",
                    "state",
                    "sessionControls",
                    "conditions",
                    "grantControls"
                ]
            }
        }
    }
}
```

In our next step, we'll look up the Azure AD exclusion group that you have in place. If you have more than one, just repeat the steps. but make sure that you rename the steps so you can recognize them later. In this example, I use only one group. Enter the Group ID.

- ![](/assets/images/image-9.png)
    
- ![](/assets/images/107-04-07-2020.png)
    

Next, we are creating a **condition**. When you add the _excludeGroups_ item to the condition field, the Apply to each action is automatically created. The value of the parsed JSON is added to the source field.

Create a condition like this example. You can add multiple conditions if needed. Compare the ExcludeGroups to the ID of the Azure AD Group that we looked up earlier. So if the policy has not excluded the specific group, the condition outcome is **_true_**, which will trigger our last and final step: the email notification.

![](/assets/images/image-10.png)

Prepare the email to your needs. You can use all the other values to compose the email. To give you an idea, here's mine:

![](/assets/images/image-11.png)

Your flow will look something like this:

![](/assets/images/image-14.png)

If you run the flow and you got a policy in place with the missing group, an email like this is send:

![](/assets/images/image-12.png)

## Let's wrap up

In this short blog post, I showed how you can govern your Conditional Access policies with the use of Power Automate or Logic Apps. Of course, this is reactive instead of proactive, but it might save you a lot of trouble someday. You can use this concept in many use cases. This one is meant to make sure that your exclusion groups are always excluded from the policies.

Stay safe!
