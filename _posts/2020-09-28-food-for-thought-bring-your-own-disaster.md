---
title: "Food for thought - Bring Your Own Disaster."
date: 2020-09-28
categories: 
  - "intune"
  - "security"
tags: 
  - "app-protection"
  - "byod"
  - "intune"
  - "mam"
  - "mdm"
image: "/assests/images/pexels-pixabay-207589-scaled.jpg"
---

Today a slightly different blog post. It's a common discussion that I face almost daily. Clients that embrace the "_anywhere, anytime, any device_" approach, and want to take control over their data. And that's not as easy as it sounds.

## The problem

One of the most common challenges that organizations face when embracing the modern workplace, is the one with Bring Your Own Devices. Personal devices. Devices in all flavors and sizes. Devices from different hardware vendors, with different operating systems, and patch levels.

When you think of Bring Your Own Devices, you often think of laptops, desktops, and mobile devices. Let's start with the hardest one: laptops and desktops.

These devices are multifunctional. They are used by parents to work on during the day, used by kids for social media and Netflix at night, and used to present some slides on a local volunteer event at the weekends. You get the idea.

What you don't know about these devices is the following:

- Is the device protected with a (strong) password or PIN?
- Is the hard disk encrypted?
- Is Secure Boot enabled?
- Is antivirus software installed?
- Is that antivirus software up to date?
- What other software is installed on that machine?
- Is the device infected with malware?
- What's the patch level of the device?

In order to know that, you have to manage these devices, and check whether these devices are compliant. But that's the main cause of this problem: do you want to manage personal devices, and are your employees willing to enroll their devices into your (Mobile) Device Management software?

## Protect your data

Besides the devices itself, you have another big challenge when it comes to BYOD. How do you protect your corporate data? Let's talk about Microsoft Teams for example. Microsoft Teams is the front door to basically all your Office 365 services. The main apps that are integrated with Microsoft Teams are SharePoint and Onedrive. They store the channel data and the files that you send over by using the chat feature.

![Overview of security and compliance - Microsoft Teams | Microsoft Docs](/assets/images/overview_of_security_and_compliance_in_microsoft_teams_image1.png)

By default, every user can connect to Microsoft Teams by using the web browser and desktop application, and can then go on and **sync** the document libraries and Onedrive content to their devices. Once that data is downloaded to the device, you pretty much lost control over that data. Users can for example:

- Copy files to USB drives
- Email files with personal email accounts
- Upload files to cloud storage like Google Drive or Dropbox
- Print files

![](/assets/images/image-26.png)

## Native protection SharePoint

In my opinion, a personal device should **never** have full access to your data. You should at least configure some limitations. The easiest way is to configure the access control settings for SharePoint. Here you can limit the access for unmanaged devices. [Peter](https://www.petervanderwoude.nl/post/accessing-sharepoint-and-onedrive-content-on-unmanaged-devices/) and [Kenneth](https://www.vansurksum.com/2020/06/26/limit-access-to-outlook-web-access-and-sharepoint-online-and-onedrive-using-conditional-access-app-enforced-restrictions/) already did a great write up on this. Check it out to get started.

![](/assets/images/image-24.png)

By using this setting you can limit or block the access from unmanaged devices. Take note that this will also impact Microsoft Teams and other services that rely on SharePoint, such as Onedrive.

## Windows Information Protection without enrollment

For Bring Your Own Devices that run Windows 10 you can configure Windows Information Protection to prevent company data leak to personal apps and services. WIP-WE, also known as MAM for Windows 10 is not waterproof and is difficult to implement.

Microsoft recently announced [Endpoint Data Loss Prevention](https://techcommunity.microsoft.com/t5/microsoft-security-and/announcing-public-preview-of-microsoft-endpoint-data-loss/ba-p/1534085). This looks pretty promising, but your devices have to be Azure AD joined or hybrid joined. Next to that, it requires a Microsoft 365 E5/A5 license.

One thing to keep in mind that this will only cover your Windows 10 BYOD devices. Other operating systems don't work with WIP, MIP, or EDLP.

## Cloud App Security

![](/assets/images/image-25-787x1024.png)

Cloud App Security can cover a [lot of use-cases](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3nibJ). One of them is: Protect your data when it’s downloaded to unmanaged devices. Using a reverse proxy, the data can be either protected or blocked when downloaded managed devices. To see how this works, read my [previous blog](https://janbakker.tech/control-access-from-unmanaged-devices-with-cloud-app-security/) post about this.

One thing to keep in mind is that this only works with modern browsers. Desktop apps can only be controlled using [access policies](https://janbakker.tech/block-outdated-operating-systems-with-cloud-app-security/), but they cannot control the data.

When using this feature, you can have full control of your data, whether it's downloaded from, or uploaded to your Microsoft cloud.

To make this bulletproof you should also block access from unmanaged devices for desktop apps using Conditional Access.

## Mobile devices

Now, let's talk about mobile devices, like phones and tablets. A lot of organizations allow users to use mobile apps to access corporate data. This is a very common use-case. And this doesn't have to be a problem.

Using Intune App Protection policies (also known as MAM), you can prevent your corporate data leaking to personal apps, and protect the app with a strong PIN. Since you don't manage the device, you cannot force users to set a PIN on the device itself. Corporate data is separated from personal data, even within apps itself. Data at rest is encrypted, and admins can do a remote (selective) wipe to clean the corporate data from the device. The personal data is not touched. Check out this [blog post](https://www.inthecloud247.com/force-outlook-on-ios-and-android/) to get started.

![Conceptual image that shows company data being protected by policies](/assets/images/apps-with-protection-policies.png)

Be careful with implementing these policies. You can easily break productivity of your users. A few tips:

- Start with picking the [right level](https://docs.microsoft.com/en-us/mem/intune/apps/app-protection-framework#level-1-enterprise-basic-data-protection) of protection. Don't kill productivity.
- Allow a few characters that users can copy between apps. Think of an address for navigation, or a phone number that you need to copy from an email or Teams chat.
- Instruct your users, especially Android users. MAM for Android requires the Company Portal app to be installed on the device. No need to enroll (common helpdesk question), just have to sit on the device.
- Block rooted and jailbroken devices. These devices can contain apps that break the DLP feature from MAM.
- Don't allow 3rd party apps to connect to Office365 if you don't have the proper protection in place. Only allow [approved client apps](https://docs.microsoft.com/en-us/azure/active-directory/conditional-access/concept-conditional-access-grant#require-approved-client-app).

## Wrap things up

Yes, you'll need an expensive A5 / E5 licenses for a lot of these features. I hear that a lot. But hear me out. When you want to deal with Bring Your Own Devices, you have to take extra steps to protect your data, identity, and devices. If you don't want that, just buy all your employees a **managed** (mobile) device. It's that simple.

You still have to protect your data, but it's easier, and will probably cost less in the end. Of course, this depends on the use-case, but it's good to fully understand what it takes when you support BYOD scenarios.

Stay safe!
