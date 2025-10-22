---
title: "How to keep an eye on your Teams with Log Analytics and Azure Monitor?"
date: 2020-04-26
categories: 
  - "entra"
  - "security"
tags: 
  - "alerts"
  - "azure-monitor"
  - "changes"
  - "kql"
  - "log-analytics"
  - "logs"
  - "monitor"
  - "queries"
  - "teams"
  - "workspace"
image: "/assests/images/person-in-red-and-brown-jacket-holding-magnifying-glass-3965671-scaled.jpg"
---

In my [previous blog post](https://janbakker.tech/2020/04/19/activity-policy-templates-for-teams-in-microsoft-cloud-app-security/), I wrote about the new Teams activity policy templates in Cloud App Security. A great addition to easily keep an eye on your teams. Let's take a short look a the policies before we continue. The policies will create alerts when:

- a team’s access level is changed from private to public
- an external user is added to a team
- a user deletes a large number of teams

These templates are easy to use, and will only take a couple of minutes to configure. But what if you don't have the licenses for Cloud App Security? Is an (expensive) E5 license your only way out? Let's get this straight. As a cloud (security) consultant it is not my main job to cross- or upsell organizations' licenses. Sometimes you have to be creative. Spending a lot of money on products that you not fully utilize, is money down the drain. Let's see if I can create the same alerts without the use of Cloud App Security.

I used Log Analytics and Azure Monitor alerts to create three equivalent polices. This is also to show you what's possible. This can be used in many, many use cases. Today, we focus on Microsoft Teams, just like the new activity policies in Cloud App Security.

![](/assets/images/image-46.png)

#### Requirements & pricing

For the use of Log Analytics, you'll need a subscription. The good thing about Log Analytics pricing is that you can choose for a pay-as-you-go pricing model. You pay for data ingestion and retention. The first 5 GB data ingestion is free and you can send up to a thousand alerts without charge. As you create your workspace, you'll have a good overview of your [estimated costs](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/manage-cost-storage) and the amount of data is handled. I suggest you take a look at [this page](https://azure.microsoft.com/en-us/pricing/details/monitor/) for further details on pricing.

#### Log Analytics

To start off, if you don't have a Log Analytics Workspace yet, please create one. Choose your pricing tier and region in the wizard.

![](/assets/images/msedge_6XrEylb6YI-1.png)

Next, we'll make sure that our Azure AD audit data is sent to Log Analytics. In order to that, head over to **Azure Active Directory -> Diagnostic settings** and choose **\+ Add diagnostic setting**

![](/assets/images/msedge_DeUXuVw8Zr.png)

For this example, we just need our **audit logs**, but you can choose to push both audit and sign-in data to your workspace. Sign-in logs require additional licenses. For now, we just need the audit logs.

![](/assets/images/msedge_lq7wDUMN2h.png)

#### Alert rules

Now that the data is stored in Log Analytics, we can go on and create our alert rules. Head over to your Log Analytics workspace and go to **Alerts.** Next, choose **\+ New alert rule** to create our first rule.

Let's start with the rule to detect if a **_Team is changed from private to public_**. The steps for the remaining policies are almost the same. An overview of all the setting is listed below.

![](/assets/images/image-39.png)

For our condition, we use a **Custom log search**.

![](/assets/images/image-40.png)

In the search query field, add the following (KQL) query:

```
AuditLogs
| where ActivityDisplayName == "Update group"
| where TargetResources[0].groupType == "unifiedGroups" 
| where parse_json(tostring(TargetResources[0].modifiedProperties))[0].displayName == "IsPublic" 
| where parse_json(tostring(parse_json(tostring(TargetResources[0].modifiedProperties))[0].newValue))[0] == true
| extend Team_ = tostring(TargetResources[0].displayName) 
| extend displayName_ = tostring(parse_json(tostring(InitiatedBy.user)).displayName) 
| extend userPrincipalName_ = tostring(parse_json(tostring(InitiatedBy.user)).userPrincipalName)
| project ActivityDateTime, ActivityDisplayName, Team_, displayName_, userPrincipalName_
```

In this example, I filtered out most of the output and narrowed it down to a few columns, so that your alert mail will be much cleaner. In this example, I only parse the date and name of the activity, the team that is changed, and the display name and UPN of the user that changed it. Of course, you can change this to whatever your needs are. For now, we leave it like this.

Next, choose the alert logic and the interval for this query. I want my alert to kick off at 1 or more results. The query has to run every 60 minutes. Take note of the "count" parameter that is added at the end of your query. This is automatically added because we use the alert logic "number of results". Basically, we're just counting events and trigger the alert when the threshold is reached.

Again, you can change this if you want this to run more or less frequent.

![](/assets/images/image-42.png)

Now we have configured our query, we have to select an action. Assuming this is your first Action Group, we are going to create a new one. Otherwise, just pick your existing action group. In this example, I create an action group to alert my OPS team using Email

![](/assets/images/image-43.png)

To create the rule, pick a name and description for your alert, and select the severity level you want. You can also choose to suppress the alerts or adjust the email subject if you want.

![](/assets/images/image-44.png)

Now that we created our first alert, we can continue and create the remaining two alerts. Because the steps are almost the same, I give you an overview of the settings I used.

<figure>

| Setting | Value |
| --- | --- |
| Name | **Mass deletion (Teams)** |
| Description | This policy is triggered when a user deletes a large number of teams. |
| Query | AuditLogs   \| where ActivityDisplayName == "Delete group"   \| where Identity == "Microsoft Teams Services"   \| extend team\_ = tostring(TargetResources\[0\].displayName)   \| extend displayName\_ = tostring(parse\_json(tostring(InitiatedBy.user)).displayName)   \| extend userPrincipalName\_ = tostring(parse\_json(tostring(InitiatedBy.user)).userPrincipalName)   \| project ActivityDateTime, ActivityDisplayName, displayName\_, userPrincipalName\_, team\_ |
| Based on | Number of results |
| Operator | Greater than |
| Threshold value | 20 |
| Period in minutes | 60 |
| Frequency in minutes | 60 |

<figcaption>

You have to determine what threshold value would works for you. In this example, the alert is triggered when 20 or more teams are deleted in 1 hour.

</figcaption>

</figure>

<figure>

| Setting | Value |
| --- | --- |
| Name | **External user added (Teams)** |
| Description | This policy is triggered when an external user is added to a team. |
| Query | AuditLogs   \| where ActivityDisplayName == "Add member to group"   \| where TargetResources\[0\].userPrincipalName contains "#EXT#"   \| where Identity == "Microsoft Teams Services"   \| extend ExternalUPN = tostring(TargetResources\[0\].userPrincipalName)   \| extend Team = tostring(parse\_json(tostring(parse\_json(tostring(TargetResources\[0\].modifiedProperties))\[1\].newValue)))   \| extend displayName\_ = tostring(parse\_json(tostring(InitiatedBy.user)).displayName)   \| extend userPrincipalName\_ = tostring(parse\_json(tostring(InitiatedBy.user)).userPrincipalName)   \| project ActivityDateTime, ActivityDisplayName, Team, ExternalUPN, displayName\_, userPrincipalName\_ |
| Based on | Number of results |
| Operator | Greater than |
| Threshold value | 1 |
| Period in minutes | 60 |
| Frequency in minutes | 60 |

<figcaption>

To find external users added to a Teams, I use this query that looks for the **#EXT#** value in the UserPrincipalName. As far as I could see the audit logs did not specify a guest attribute that I could use. This will work for now, but maybe a better way comes along soon.

</figcaption>

</figure>

| Setting | Value |
| --- | --- |
| Name | **Access level change (Teams)** |
| Description | This policy is triggered when a team's access level is changed from private to public. |
| Query | AuditLogs   \| where ActivityDisplayName == "Update group"   \| where TargetResources\[0\].groupType == "unifiedGroups"   \| where parse\_json(tostring(TargetResources\[0\].modifiedProperties))\[0\].displayName == "IsPublic"   \| where parse\_json(tostring(parse\_json(tostring(TargetResources\[0\].modifiedProperties))\[0\].newValue))\[0\] == true   \| extend Team\_ = tostring(TargetResources\[0\].displayName)   \| extend displayName\_ = tostring(parse\_json(tostring(InitiatedBy.user)).displayName)   \| extend userPrincipalName\_ = tostring(parse\_json(tostring(InitiatedBy.user)).userPrincipalName)   \| project ActivityDateTime, ActivityDisplayName, Team\_, displayName\_, userPrincipalName\_ |
| Based on | Number of results |
| Operator | Greater than |
| Threshold value | 1 |
| Period in minutes | 60 |
| Frequency in minutes | 60 |

#### Alert emails

To give you an idea, this is how an alert email will look like. The email will show an overview of the alert query and the top 10 results.

![](/assets/images/image-45.png)

#### Let's wrap up

This blog post will give you an idea of what is possible with Log Analytics, KQL queries, and Azure Monitor Alert rules. This is the most basic setup, but keep in mind that you can search for any event and trigger any workload. You can kick off workbooks, playbooks, Power Automate Flows, webhooks, Logic Apps, Azure Functions, and so on. The sky is the limit here.

![](/assets/images/Drake-Hotline-Bling-1024x1024.jpg)

In this example, we only used the audit logs, but you can ingest any data into Log Analytics and query for results. If you want more information, start reading [here.](https://docs.microsoft.com/en-us/azure/azure-monitor/log-query/log-query-overview) Before this blog post, I had **never** written any KQL queries. So just get started and see how far you can come.

Also, hopefully this will give you ideas on what is possible, even if you don't have proper licenses or budget for security products.

Stay safe!
