---
title: "Activity policy templates for Teams in Microsoft Cloud App Security"
date: 2020-04-19
categories: 
  - "security"
tags: 
  - "access-level-change"
  - "cloud-app-security"
  - "external-users"
  - "mass-deletion"
  - "policy-templates"
  - "security"
  - "teams"
image: "/assests/images/image-30.png"
---

The usage of Teams is massively increased over the past weeks. The numbers of people using Teams nowadays are insane. Organizations rapidly enrolled Teams to their users. But what about security? Time to take a look at security and governance. But where do you start?

In my opinion, Microsoft Cloud App Security gives you the best insights on where to focus. Using the MCAS activity log you can study the users' behavior. What is happening? What files are downloaded? What 3rd party apps are used within Teams? From this point, you can adjust your security settings or take other actions to tighten your security around Teams.

Today we take a look at the recently released activity policy templates. These templates can create an alert when a specific event happens. In the Teams section, there are tree templates available:

- **Access level change (Teams):** Alerts when a team's access level is changed from private to public.
- **External user added (Teams):** Alerts when an external user is added to a team.
- **Mass deletion (Teams):** Alerts when a user deletes a large number of teams.

![](/assets/images/image-30.png)

You can create your own policies based on these templates. This is pretty straight forward. Head over to your MCAS portal. If you don't know the URL of your MCAS management portal, just go to [https://security.microsoft.com](https://security.microsoft.com) -> More resources - Microsoft Cloud App Security

![](/assets/images/image-31.png)

In the MCAS portal, click Contro -> Policies -> Create policy and select Activity policy.

![](/assets/images/msedge_HnZEC246Dd.png)

Next, pick your template. In this example, I pick the External user added (Teams) template.

![](/assets/images/image-32.png)

Configure the policy as you like. You can edit the settings to your needs. Select how you want to be alerted. You can send an alert als email, or you can send them to Power Automate to kick off a Flow.

![](/assets/images/image-34.png)

In order to test my policy, I've added a guest user to my demo team. An alert is created and an email is send to the SecOps team.

- ![](/assets/images/msedge_KBKfZHDGLh.png)
    
- ![](/assets/images/image-37.png)
    

#### Conslusion

When we look at the security around Teams, Microsoft Cloud App Security can help out in many ways. You can use it to keep an eye on what users are doing, as described in this blogpost (activity policies). This will not impact users. Next to that, you can create **access** or **session** policies. In combination with Azure AD Conditional Access, you can configure the policies for managed devices, but more important, personally owned devices.

Stay tuned for more!

If you don't own Microsoft Cloud App Security licenses, check out [this post](https://janbakker.tech/2020/04/26/how-to-keep-an-eye-on-your-teams-with-log-analytics-and-azure-monitor/). You will love it! I will show you how to make equivalent polices with Log Analytics and Azure Monitor.

[![](/assets/images/image-47.png)](https://janbakker.tech/2020/04/26/how-to-keep-an-eye-on-your-teams-with-log-analytics-and-azure-monitor/)
