---
title: "Microsoft Secure Score Series - 01 - What is Microsoft Secure Score?"
date: 2020-03-04
categories: 
  - "secure-score"
tags: 
  - "improvement"
  - "microsoft365"
  - "office365"
  - "secure-score"
  - "security"
coverImage: "image-1.png"
---

## What is Microsoft Secure Score?

Microsoft Secure Score offers a list of actions and recommendations to strengthen your security on your Office 365 workloads. Each completed action will impact your overall score. It covers SharePoint Online, Exchange Online, OneDrive for Business, Microsoft Information Protection, Azure AD, Microsoft Defender ATP, and Cloud App Security among other things. It is not 100% waterproof, but it will give you a great baseline to start with. It will help you to protect your environment from threats, or at least make it a lot more difficult for malicious actors.

#### How does it help me?

Secure Score helps organizations:

- Report on the current state of the organization’s security posture.
- Improve their security posture by providing discoverability, visibility, guidance, and control.
- Compare with benchmarks and establish key performance indicators (KPIs).

Organizations gain access to robust visualizations of metrics and trends, integration with other Microsoft products, score comparison with similar organizations, and much more. The score can also reflect when third-party solutions have addressed recommended actions.

#### Where to find it?

Secure Score is part of the Microsoft 365 Security portal. There are two different views available:

- [Released version](https://security.microsoft.com/securescore?viewid=overview)
- [Preview version](https://security.microsoft.com/securescorepreview?viewid=overview)

I personally like the preview portal better, but you can easily switch between the two versions.

![](/assets/images/image.png)

When you go into the portal, you'll land on the dashboard page. The page is roughly divided into 4 tabs.

#### Overview

In this section, you got a nice clean overview of the overall score, a list of the top improvement actions, a comparison chart where you can see how you are doing compared to other companies in the same branch. You can apply filters per category to zoom in.

![](/assets/images/image-1.png)

As you can see, we got some work to do in this tenant.

#### Improvement actions

Here you find all the recommended actions for your tenant. You can see what actions are new, how many points you can achieve by applying them and what the impact and complexity are. You can apply filters and, for example, to show only the actions that you are licensed for.

The action are categorized into 5 different areas:

- Identity
- Apps
- Data
- Device
- Infrastructure

With each improvement action, you can achieve points. You can start of with the low hanging fruit by filtering on low impact and complexity actions.

![](/assets/images/image-2.png)

#### Licenses

In the overview, you can filter on recommendations that you are already licensed for. Sometimes an action can be done in multiple ways. Enabling MFA for example, can either be done by simpling enabling MFA for each user, or you can use Azure AD Identity Protection which requires Azure AD Premium 2 license.

#### History

The history tabs shows all the activity that impacted your score. Your score changes when you work on your recommendations, or if new actions are added by Microsoft.

![](/assets/images/image-3.png)

#### Metrics & trends

In this tab you can monitor trends, for example, if your score dropped or increased. Also, you can compare yourself to other organizations and set your personal goals by setting your Secure Score zone.

![](/assets/images/image-4.png)

#### Improvement actions ranking

Ranking is based on the number of remaining points left to achieve, implementation difficulty, user impact, and complexity. The highest-ranked improvement actions have a large number of points remaining with low difficulty, user impact, and complexity.

### It's just a number...

Setting a goal can be really helpful. It's a good motivator to constantly be on top of your security, and it will keep the ball rolling. But don't get too attached to the numbers. As stated in the intro, Microsoft Secure Score is not a contest. There are no trophies to win. The only victory here is the feeling at the end of the day that you managed to keep the bad guys out for yet another day.

Stay tuned for the next articles in this series.
