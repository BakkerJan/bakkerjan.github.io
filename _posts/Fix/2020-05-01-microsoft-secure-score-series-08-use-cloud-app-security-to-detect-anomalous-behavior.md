---
title: "Microsoft Secure Score Series – 08 – Use Cloud App Security to detect anomalous behavior"
date: 2020-05-01
categories: 
  - "entra"
  - "secure-score"
  - "security"
tags: 
  - "alerts"
  - "cloud-app-security"
  - "mcas"
  - "office365"
  - "secure-score"
  - "security"
coverImage: "person-looking-at-phone-and-at-macbook-pro-1181244-scaled.jpg"
---

## Use Cloud App Security to detect anomalous behavior

_Cloud App Security anomaly detection policies provide User & Entity Behavior analytics (UEBA) and advanced threat detection across your cloud environment._

* * *

Today we take a look at Cloud App Security. I recently wrote [a blog about the new activity policies](https://janbakker.tech/2020/04/19/activity-policy-templates-for-teams-in-microsoft-cloud-app-security/) in Cloud App Security, so if your organization uses Teams, you should definitely take a look a that one.

The improvement action we're talking about has no user impact and might no increase your score right away. But if you dó own Microsoft Cloud App Security licenses, and you never have seen the portal before, this is a good moment to get started.

![](/assets/images/proxy-architecture.png)

#### What is Microsoft Cloud App Security?

As stated in the [Microsoft docs](https://docs.microsoft.com/en-us/cloud-app-security/what-is-cloud-app-security), Microsoft Cloud App Security is a Cloud Access Security Broker that supports various deployment modes including log collection, API connectors, and reverse proxy. It provides rich visibility, control over data travel, and sophisticated analytics to identify and combat cyber threats across all your Microsoft and third-party cloud services.

Microsoft Cloud App Security natively integrates with leading Microsoft solutions and is designed with security professionals in mind. It provides simple deployment, centralized management, and innovative automation capabilities.

Most of the people I talk to refer to MCAS as "the tool to discover Shadow IT". And true that is! But MCAS is so much more. To give you an idea what's possible; with Microsoft Cloud App Security you can:

- Enforce access and session controls on your organization's apps based on any condition in Conditional Access
- Detect if your users' credentials are leaked
- Detect unusual file deletion activity
- Detect suspicious inbox forwarding
- Control access and session from Bring Your Own Devices
- Ingest 3rd party firewall logs

#### How to get there?

There are a couple of ways to access your Cloud App Security portal. You can go to [https://security.microsoft.com](https://security.microsoft.com) and look for the **More resources** button. You'll see the button to access your portal. Or use [https://aka.ms/mcasportal](https://aka.ms/mcasportal) or [https://portal.cloudappsecurity.com/](https://portal.cloudappsecurity.com/)

![](/assets/images/image-31.png)

Take note of the URL of the MCAS portal and save it to your bookmarks for later use.

![](/assets/images/image-2.png)

If MCAS is not used before, you might have to do some additional setup. To start you have to connect your apps.

1. From the settings cog, select **App connectors**.
2. Click the plus sign to add an app and select an app.
3. Follow the configuration steps to connect the app.

![](/assets/images/image-5.png)

When you log in, you'll land on the Dashboard page. Here you have a brief overview of all your alerts.

![](/assets/images/image-3.png)

At the left panel, head over to the Alerts section. Here you'll find all the alerts. You can apply filters on severity, category, and app for example.

![](/assets/images/image-4.png)

#### Get started today!

If you have the licenses to use Microsoft Cloud App Security, don't hesitate and start today! MCAS can give you great insight into users activities. Try to create your first policy based on a template to get started.

1. Go to **Control** > **Templates**.
2. Select a policy template from the list, and then choose (+) **Create policy**.
3. Customize the policy (select filters, actions, and other settings), and then choose **Create**.
4. On the **Policies** tab, choose the policy to see the relevant matches (activities, files, alerts). Tip: To cover all your cloud environment security scenarios, create a policy for each **risk category**.

#### Wrap things up

To see if you are eligible to use MCAS, please take a look a the [Licensing Datasheet](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO). If you don't have access to Microsoft Cloud App Security, please take a look at this [blog I wrote earlier](https://janbakker.tech/2020/04/26/how-to-keep-an-eye-on-your-teams-with-log-analytics-and-azure-monitor/) to give you an idea of what is possible.

For now, stay safe!
