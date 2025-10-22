---
title: "Microsoft Secure Score Series – 13 – Set automated notifications for new and trending cloud applications in your organization"
date: 2020-07-02
categories: 
  - "secure-score"
  - "security"
image: "/assets/images/space-grey-ipad-air-with-graph-on-brown-wooden-table-187041-scaled.jpg"
---

## Set automated notifications for new and trending cloud applications in your organization

```
With Cloud Discovery policies, you can set alerts that notify you when new apps are detected within your organization.
```

And again, we're back at Cloud App Security. Earlier I showed how MCAS can help you to [discover shadow IT in your organization](https://janbakker.tech/2020/06/06/microsoft-secure-score-series-10-discover-trends-in-shadow-it-application-usage/) by ingesting your firewall and proxy log files. Today, we take a look at the app discovery policies that are available. If you are new to Cloud App Discovery, I suggest you read my [previous blog](https://janbakker.tech/microsoft-secure-score-series-10-discover-trends-in-shadow-it-application-usage/) first.

## What are App discovery policies?

App discovery policies can detect any new app that is being used by your users. It can also detect anomalies. There are a lot of templates available to get you started, but you can build your policy from scratch.

I've selected some random templates that you can pick from to give you an idea:

- Alert on any new app
- Alert on a new CRM or HR application
- Alert on a risky app
- Alert on a new online meeting app
- Alert on a popular app

Let's say that a company implemented **Microsoft Teams** for online meetings. At some point, the users start using Zoom instead, because they like some of the features better. (what can I say?) Using app discovery alerts, the company becomes aware of this behaviour and can take the right steps to remediate this.

## Create an app discovery policy

Head over to your [Cloud App Security Portal](https://aka.ms/mcasportal) and create a new policy. Pick the **_App discovery policy_**.

![](/assets/images/image-96.png)

You can select one of the templates, or select **no template** to build your own policy. In this example, I'll pick **_New high upload volume app_**.

![](/assets/images/image-97.png)

Select **Apply template** to load the pre-configured settings. If you are using an existing policy, all values will be replaced with the template.

![](/assets/images/image-98.png)

![](/assets/images/image-100-958x1024.png)

- 1\. You can adjust the name of the policy to make it easy to recognize.
- 2\. Select the policy severity
- 3\. If needed, add some description to clarify the purpose of the policy
- 4\. You can add additional specifications, for example to only alert on cloud storage apps.
- 5\. Select the report that the policy applies to. In this example, were are selecting the continuous reports that we created in the [previous blog](https://janbakker.tech/microsoft-secure-score-series-10-discover-trends-in-shadow-it-application-usage/).
- 6\. You can adjust the amount of data that will trigger the alert. You could also create extra conditions such as the number of IP addresses, machines, or users.

Once you specified the parameters, you can configure the alert action. You can kick off a Power Automate playbook or send an alert by email or text message.

![](/assets/images/image-103.png)

Additionally, you can take governance actions. You can tag the application to trigger other polices within Cloud App Security, for example. You can create custom tags in the Settings pane.

![](/assets/images/image-104.png)

## Cloud Discovery anomaly detection policy

You can also detect anomalies in the usage of cloud apps. This way you are able to configure alerts when the usage increases extremely compared to the regular baseline.

Add a new policy, and pick the Cloud Discovery anomaly detection policy. Again, you can pick a template to get you started or create your own policy.

- ![](/assets/images/image.png)
    
- ![](/assets/images/image-4.png)
    

Optional, define the scope of the alerts, by selecting specific apps, app categories, or risk score for example.

You can pick from all the continuous reports and select either or both Users and IP addresses. Also, you can ignore alerts before a specific date and adjust the sensitivity of the alerts. You can pick from 1-5 alerts per 1000 users or IP addresses. The alerts are triggered for the activities with the highest risk.

![](/assets/images/image-3.png)

## Wrap up

Cloud App Security is a great tool to stay on top of your applications and also discover trends among your users. Besides discovering shadow IT, MCAS is capable of a lot more. Please also read my other [MCAS related blogs](https://janbakker.tech/tag/cloud-app-security/) to learn about the other capabilities or stay tuned to feature blogs on this topic.

For more information about App discovery and alerts, reach out to the following documentation:

- [https://docs.microsoft.com/en-us/cloud-app-security/cloud-discovery-policies](https://docs.microsoft.com/en-us/cloud-app-security/cloud-discovery-policies)
- [https://docs.microsoft.com/en-us/cloud-app-security/cloud-discovery-anomaly-detection-policy](https://docs.microsoft.com/en-us/cloud-app-security/cloud-discovery-anomaly-detection-policy)

If you have any questions, please drop your comment below.

Stay safe!
