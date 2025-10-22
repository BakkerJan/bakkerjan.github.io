---
title: "Windows Update for Business. \"Just\" a free cloud service."
date: 2020-02-21
categories: 
  - "windows-10"
tags: 
  - "cloud"
  - "config-manager"
  - "intune"
  - "modern-workspace"
  - "sccm"
  - "update"
  - "windows"
  - "windows10"
  - "wsus"
  - "wufb"
image: "/assests/images/Analytics.png"
---

Since the introduction of the "Modern Workspace," a lot is changed when we talk about updates. Let's face it: staying current with Windows 10 is hard. But not as hard as, for example, maintaining Windows 7. Because back then we didn't have Windows Update for Business. Luckily for you, now we have! Let's dive in.

## What is Windows Update for Business?

There are many different ways to keep your devices up to date. In Enterprises, the most common tools out here are Config Manager (good ol' SCCM) and Windows Server Updates Services, better known as WSUS. But now there is another player in the field: Windows Update for Business, also known as 'WUfB'. WUfB is a free "cloud service" hosted by Microsoft to keep Windows 10 devices up to date. Technically, it's not a cloud service, but I personally would describe it as a bundle of settings to design your update strategy. This service is built specifically for Windows 10. The settings can be managed by group policies, or from your MDM solution, Intune for example. If you want to use WUfB, no on-premises infrastructure is needed. The updates will be delivered to your Windows 10 devices by these super-fast update servers in the cloud. In the early days, WufB was limited, but nowadays, the features are rapidly growing.

## Update types

To start off, let's take a look at the way Windows 10 updates work. There are different types of updates.

**Quality updates**

These types of updates contain patches, bugfixes, drivers and small adjustments. As usual, these types of updates are delivered on Patch Tuesday (second Tuesday of the month). However, Microsoft also releases so-called C and D releases, usually delivered in the 3rd or 4th week of the month. These types of updates can be delivered anytime to patch vulnerabilities or risks. These updates are cumulative updates, meaning that all updates that have been released earlier, will be superseded by the next one.

**Feature updates**

Feature updates are released semiannual, normally during spring and fall. These updates containing significant changes and bring more features to the operating system. Feature updates are relatively large and can have a big impact. In fact, this is where I see companies struggle the most.

**Driver updates**

This type of update contains non-Microsoft drivers. Administrators can turn off this feature.

**Microsoft updates**

These types of updates contain updates for other Microsoft products, such as Office. Administrators can also turn off these types of updates.

![](/assets/images/Update20type.png)

WUfB has a large scale of settings that can be configured. The settings are speaking for themselves, but to design a good strategy, its good to have some background knowledge.

## Update channels

WUfB offers multiple update channels. The channel basically controls when updates are being pushed to the clients. These channels had multiple names in the past, such as Current Branch (CB) and Current Branch for Business (CCB). Most recently these channels where named Semi-annual Channel (SAC) and Semi-annual Channel Targeted (SAC-T), but Microsoft has decided to [simplify these channels](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Windows-Update-for-Business-and-the-retirement-of-SAC-T/ba-p/339523). They deprecated the SAC-T channel as from Windows 10 1903. Now only the SAC channel remains.

Apart from that, you can also opt-in on the so-called Insider Preview channels. These channels provide updates who are still in progress. You can choose different kinds of channels within the Insider Preview Program.

The channels are divided as follows:

**Windows Insider Fast**

The Fast ring pushes updates that are tested by Microsoft. However, these updates can contain bugs as they are not tested broadly. Devices using the Insider Fast ring are somehow unstable and this channel is meant to collect feedback from the field or to test against your applications on forehand. Updates are pushed once or twice a week.

**Windows Insider Slow**

When the first feedback is collected and processed, updates are pushed to this ring. This channel gets more stable updates but can still contain bugs, which can make the device unstable. Updates are pushed once a month.

**Windows Insider Release Preview**

Updates in this channel are almost ready for broadly roll-out. You can choose this ring to test out new features but also work on a stable device.

**Semi-annual Channel**

When updates are thoroughly tested, they are being released through the public channel. However, sometimes an update is pulled back after release because of a major problem. To be one step ahead of this, you can set the deferral period for this channel. More on that later.

Quality updates                         defer up to **30** days

Feature updates                       defer up to **365** days

The deferral period is calculated from the day the update is launched. [Here](https://docs.microsoft.com/en-us/windows/release-information/) you can find an overview of the Windows 10 releases.

**Register Insider Preview for your domain**

In order to use the Insider Preview in your company, you have to sign up your domain first. After that, your users can opt-in the Insider channels, or you can configure it for them by using Intune for example. In order to receive Insider Preview builds, devices must be joined to the same Azure AD domain that was registered with the Windows Insider Program. For more information, reach out to [this page.](https://insider.windows.com/en-us/for-business-organization-admin/)

## Configure update rings for Windows 10

It can be quite challenging to keep your devices up to date. On the one hand, because your users don't have time to finish these updates from time to time. It also frustrates people when they cannot use their devices for a while, and sometimes after updates things just break.

That's why it's a good idea to use update rings. By creating these different rings, you can roll out an update more gradually in your organization. For example, you can select a subset of users and assign them to an Insider ring. In that case, if something goes wrong, you can fix this before it reaches the broader rings. Select a so-called "early adopters group" in order to catch the first problems. Try to select a varied set of users, so you can test most of your applications.

This way you can thoroughly test an update before it hits the broader rings. This example illustrates a possible strategy for your update rings:

![Example - Update rings for feature updates](/assets/images/Rings-1-1.png)

For quality updates, you can have the same strategy. Since these updates can only be deferred by 30 days, you should handle more quicker in case something goes wrong. The next chapter will discuss this further. Of course, the way of building your rings depends on what kind of organization you work with. This example is just to give you an idea of what is possible.

## Pause, deferral, and deadlines

Stay current is not that simple. It's an ongoing process and there is a much different approach than Windows 7 for example. For that reason, Windows 10 is often called "Windows as a Service".

Unlike WSUS or Config Manager, it is not possible to approve or deny updates using WufB. If Microsoft releases the updates, they are being offered to the clients right away. You can tell your clients to reach out for updates on a specific date and/or time. Also, you can configure your working hours, so your users are not bothered during the workday.

## Defer updates

Using WufB you are able to defer updates for a period of time. You can separately defer quality and feature updates. You can use the same update rings as described in the previous chapter. Each ring has a different deferral period, so you can gradually roll out updates in your organization.

![Example - Update rings for quality updates](/assets/images/Rings-2.png)

## Pause updates

Sometimes updates can cause unexpected problems. Also, some organizations need a change freeze during the busiest time in the year. In that case, you can pause your update rings. You can pause up to 35 days. During that period, no updates are installed on your devices. Administrators can even allow their users to pause updates individually. Quality and feature updates can be paused separately.

## Deadline compliance and reboots

Windows 10 1903 brought some significant improvements to the end-user experience. Users are being informed when a device needs a reboot for updates to install. They can ignore this for a period of time, and they can pick a time that fits them best. Administrators can configure deadlines. The deadline forces the updates to install before the deadline expires. In the meantime, users can ignore the updates. The deadline is calculated from the day of release. In previous versions of Windows 10, the deadline was calculated from the moment the device came into the pending reboot state. [Learn more.](https://docs.microsoft.com/en-us/windows/deployment/update/wufb-compliancedeadlines)

![Pending restart](/assets/images/wufb-update-deadline-warning.png)

Administrators can configure deadlines and grace periods for both quality and feature updates.

## Delivery Optimization

Using WufB for your Windows 10 devices can cause a tremendous impact on your bandwidth usage. Each and every client have to download the updates individually from the Microsoft update servers in order to install them. Using Delivery Optimization, clients can share updates over the local network using peer to peer technology. Administrators can also configure how many bandwidth the devices can use for this task. [Learn more.](https://docs.microsoft.com/en-us/windows/deployment/update/waas-optimize-windows-10-updates)

![](/assets/images/DO2.png)

Delivery Optimization can also be used for apps that are downloaded from the Microsoft Store. Downloaded files are stored on the device for a short while and automatically cleaned up afterward. DO is builtin into Windows 10 and can be easily configured using group policies or Intune.

## Intune, WSUS and Config Manager

Compared to WSUS and Config Manager, WufB is more a set-and-forget kinda service. If you design a good strategy to stay current you can spend your time monitoring the progress and intervene when needed. Approving and staging updates are a thing of the past.

Most enterprises, however, are now using Config Manager or WSUS to maintain their devices. WUfB can be integrated with those services. Migrating from Config Manager to WUfB can be a complex thing to do in one step. Also, many administrators have to get used to the fact that they lose some control when they hand things over to WUfB. Using WufB can be the first step in the Modern Management approach, and that may take some time to get used to. [Learn more.](https://docs.microsoft.com/en-us/windows/deployment/update/waas-integrate-wufb)

If your planning on integrating WufB with Config Manager you can now install Delivery Optimization In-Network Cache server on your distribution points. [Learn more.](https://docs.microsoft.com/en-us/configmgr/core/get-started/2019/technical-preview-1903#bkmk_doinc)

As mentioned earlier, WUfB can be controlled in many ways. You can set Group Policies, use Config Manager, Intune, or other MDM solution you like. WUfB can even be configured trough registry settings directly.

![](/assets/images/WUfB.png)

WUfB perfectly fits in an IT landscape that is moving forward towards the Modern Workspace. WUfB can help administrators to take more control over the Windows 10 by stepping back and letting WUfB doing all the work. Also good to mention, if you are going WUfB-only, on-premises infrastructure is no longer needed to deploy these updates.

## Monitoring and reports

WUfB has no built-in reporting or monitoring. When you decide to integrate with WSUS of Config Manager, you can have an eye on your system from there. Also, it's possible to integrate with Windows Analytics. Windows Analytics has an Update Compliance module where you can check the status of your devices. Next to that, you can use the module Upgrade Readiness. In order to use this, Windows uses default telemetry. Starting with Windows 10 1903, WUfB works with level 0 (Secure), but if you want to use Update Compliance, level 1 (Basic) is needed. [Learn more.](https://docs.microsoft.com/en-us/windows/deployment/update/update-compliance-get-started)

![Monitoring flow](/assets/images/Analytics.png)

## (Tip!) Windows 10 feature updates (Preview)

When you are using Intune, you can make use of this cool new feature called Windows 10 feature updates (Preview). With this feature, you can configure your devices to stick on specific feature release. This can really help you if you don't want to install feature updates every 6 months. Peter van der Woude wrote an [excellent blog post](https://www.petervanderwoude.nl/post/controlling-windows-10-feature-updates/) about this. Go check it out if you want to get started on that.

* * *

## Wrap things up

WUfB is free to use for Windows 10 and easy to configure, without the need to invest in extra hardware. It can be configured in many ways, to the need of your organization. Together with Delivery Optimization, it is a solid service to provide updates for your (mobile) Windows 10 devices. It gives users more flexibility and takes the heat of your administrators. Using WUfB can decrease the amount of work that you spend on keeping your devices up to date. It is not a one on one replacement for your current setup. It's a much different approach. No more approving updates. If that scares you, get started with a pilot ASAP.

Either way, a lot is changed since Windows 10 has arrived. Updates are coming more frequently and on a regular base. New features are introduced semi-annual to provide optimal use of your hardware. You cannot sit back and just wait for the next update.

**_Stay current!_**
