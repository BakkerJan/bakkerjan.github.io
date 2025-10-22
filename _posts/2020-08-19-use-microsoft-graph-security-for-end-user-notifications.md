---
title: "Use Microsoft Graph Security for end-user notifications"
date: 2020-08-19
categories: 
  - "logic-apps"
  - "power-platform"
  - "security"
tags: 
  - "azure-ad-identity-protection"
  - "cloud-app-security"
  - "flow"
  - "graph-api"
  - "notifications"
  - "power-automate"
coverImage: "pexels-mentatdgt-1220757.jpg"
---

In this short blog post, I want to show how you can use the Microsoft Graph Security to send alerts and notifications to your end-users. I also want to show you that it is super easy to set up. All you need is:

- Power Automate or Logic Apps
- Microsoft Graph Security Connector (premium)
- Microsoft Teams or Email connector to send the messages
- One of the (Microsoft) security products to work with like Cloud App Security or Identity Protection

## Microsoft Graph Security

The Microsoft Graph Security connector uses the Microsoft Graph Security API. The API connects different security products and providers and puts them together in a unified schema. The schema is really easy to work with and provides a lot of information. In this example, I've picked 2 vendors to work with: **_Microsoft Cloud App Security_** and **_Azure Active Directory Identity Protection_**. These products will give some valuable information that is also suitable for end-users.

The connector provides two triggers to work with. This is where you can start scoping the alerts that you want to work with. You can pick all alerts or just the alerts with high severity.

![](/assets/images/image-8.png)

To get an idea of all the alerts, you can use the [Graph Explorer](https://aka.ms/ge). Use the **_https://graph.microsoft.com/v1.0/security/alerts_** API to go trough your alerts and see which ones to can act on. You can [use filters](https://docs.microsoft.com/en-us/graph/api/alert-list?view=graph-rest-1.0&tabs=http#optional-query-parameters) to narrow down the alerts that you are interested in.

![](/assets/images/image-9.png)

## Build the flow

So, lets build the flow. Start with an empty flow and pick your trigger from the Microsoft Graph Security connector.

In this case, I start with all the alerts. I use the Control / Switch feature to filter the alerts. You can start with the vendor name, and after that, you can use another switch to filter on category.

![](/assets/images/image-10-1024x314.png)

I collected some alerts that you can use as a reference, to get started.

| Provider | Category | Description | Title |
| --- | --- | --- | --- |
| IPC | AnonymousLogin | Sign-in from an anonymous IP address (e.g. Tor browser, anonymizer VPNs) | Anonymous IP address |
| IPC | UnfamiliarLocation | Sign-in with properties we‘ve not seen recently for the given user | Unfamiliar sign-in properties |
| IPC | ImpossibleTravel | Sign-in from an atypical location based on the user’s recent sign-ins | Atypical travel |
| IPC | InfectedDeviceLogin | Sign-in from a malware linked IP address | Malware linked IP address |
| MCAS | MCAS\_ALERT\_ANUBIS\_DETECTION\_RISKY\_IP\_ANONYMOUS | The anonymous proxy IP address \[IP\] was accessed by \[User\]   Or   A failed sign in was detected from an anonymous proxyThe anonymous proxy IP address \[IP\] was accessed by \[User\]. | Activity from an anonymous proxy |
| MCAS | MCAS\_ALERT\_ANUBIS\_DETECTION\_VELOCITY | The user \[User\] performed an impossible travel activity. The user was active from \[IP\] in \[Country\] and \[IP\] in \[Country\] within \[x\] minutes. | Impossible travel activity |
| MCAS | MCAS\_ALERT\_ANUBIS\_DETECTION\_NEW\_COUNTRY | \[User\] performed an activity. No activity was performed in \[Country\] in the past \[x\] days. | Activity from infrequent country |
| MCAS | MCAS\_ALERT\_ANUBIS\_DETECTION\_REPEATED\_ACTIVITY\_DELETE | The user \[User\] deleted more than \[X\] unique objects in a single session. | Mass delete |
| MCAS | MCAS\_ALERT\_ANUBIS\_DETECTION\_INBOX\_HIDING | A suspicious inbox rule was set on a user's inbox. This may indicate that the user account is compromised, and that the mailbox is being used to distribute spam and malware in your organization. The user \[User\] created a MoveToFolder rule named \[RuleName\] on their own inbox, to move messages to a folder named \[Foldername\]. | Suspicious inbox manipulation rule |

## Notifications

Next, you can pick your notification of choice. You can use the email, a chat message on Teams, or even a text with the use of a 3rd party connector. I suggest you start small. You don't want to SPAM your end-users, and only provide notifications that make sense. To get the email address of the user, I first use the **Get User** action. I use the User Principal Name from the alert as input. Here is an example of an email that you can compose to your users:

![](/assets/images/585-19-08-2020.png)

Or you can send a message using Teams.

![](/assets/images/587-19-08-2020.png)

Here's the result.

![](/assets/images/image-12.png)

![](/assets/images/588-19-08-2020.png)

## Example flow

I exported my flow, so that you can use it as a sample. You can [download it from Github](https://github.com/BakkerJan/Power-Automate/blob/master/End-user%20notifications/End-usernotifications_20200819130441.zip).

## Let's wrap up

It's so cool to integrate Microsoft products and make the most out of it. I made this blog post to give you some ideas of what you can do. You can go nuts here! But, as I said before, you might want to take it slow on the number of messages you send to your end-users. You could also start with high severity alerts.

Here's some documentation to get you started:

[https://docs.microsoft.com/en-us/graph/api/alert-list?view=graph-rest-1.0&tabs=http](https://docs.microsoft.com/en-us/graph/api/alert-list?view=graph-rest-1.0&tabs=http)  
[https://docs.microsoft.com/en-us/graph/security-concept-overview](https://docs.microsoft.com/en-us/graph/security-concept-overview)  
[https://docs.microsoft.com/en-us/azure/active-directory/identity-protection/howto-identity-protection-simulate-risk](https://docs.microsoft.com/en-us/azure/active-directory/identity-protection/howto-identity-protection-simulate-risk)

Stay safe!
