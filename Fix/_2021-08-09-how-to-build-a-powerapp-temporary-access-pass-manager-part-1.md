---
title: "How to build a PowerApp - Temporary Access Pass Manager - Part 1"
date: 2021-08-09
categories: 
  - "entra"
  - "power-platform"
  - "security"
coverImage: "1628535438.png"
---

## Part 1 - Introduction and overview

When I learned about Graph API, and how to use it together with Power Apps and Power Automate, a whole new world of possibilities opened up. Being able to pull data out of Graph API and use this data within PowerApps is the key to creative solutions built around and on top of Microsoft 365. The gap between a developer and a technical or functional M365 consultant becomes closer, as a lot of automation can be done with these tools as well. You don't have to be a PowerShell guru these days to do some magical automation and development. Just connect the dots with low/no-code solutions does the trick.

## My learning experience

When I learned this stuff myself, I went through a big learning curve. It took me some time to figure it all out, and there is still a lot to discover. As an example, I will show the app that I've built with PowerApps when I start learning. The data that you see is pulled out of Graph API, using a custom connector. The app is mainly built around Identity and Access information in Azure Active Directory, but you can create apps around all topics within Microsoft 365 and even talk to API's from third-party applications.

![](/assets/images/image-79.png)

If you want to learn more about this app, I recommend you to watch [this](http://<iframe width="560" height="315" src="https://www.youtube.com/embed/5yMxz_oQfNg" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>) and [this](https://www.youtube.com/watch?v=LMujYi2U4b8) video. I was a guest on the [425 show](https://www.youtube.com/channel/UCIPMDupgTRsJY5sxcdBEtCg) with [Daniel Stefaniak](https://twitter.com/d0m3l?lang=en) where we talked about this very app. Want to build your own? Keep reading!

In this series, we are going to learn the concept behind this Power App and even build our very own app. When you are able to get the concept, you can build any app for any usecase. This series is divided into 5 sections with an optional bonus part. It is recommended to read the 5 parts in this order, to build your app step by step.

- Part 1: Introduction and overview (this post)
- Part 2: App registration and Graph API permissions
- Part 3: Graph API and Graph Explorer
- Part 4: Build a custom connector based on the Graph API
- Part 5: Create an app in PowerApps using a custom connector
- (Bonus) Part 6: Integrate with Power Automate

I will show you how to build these components step by step. At the end of the series, you should be able to build your own app, based on Graph API, using a custom connector.

![](/assets/images/Overview-Custom-Connector-Graph-API-Power-Platform.png)

## What are we building?

I picked a subject that might become or already is very relevant to organizations using Microsoft 365: Temporary Access Pass. If you don't know what it is, please read [this blog post](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/temporary-access-pass-is-now-in-public-preview/ba-p/1994702) first. This tool might be helpful for your IT staff to support your (remote) workers. The app will be able to create and delete a Temporary Access Pass for the selected user. The user can be any user in your Azure Active Directory or Office 365 environment. As a bonus, I will show you how to use Power Automate to send the Temporary Access Pass by text.

The app that we are building is going to look like this:

![](/assets/images/image-72.png)

That looks awesome right?

When you feel ready, let's start building! --> [Part 2: App registration and Graph API permissions](https://janbakker.tech/how-to-build-a-powerapp-temporary-access-pass-manager-part-2/)
