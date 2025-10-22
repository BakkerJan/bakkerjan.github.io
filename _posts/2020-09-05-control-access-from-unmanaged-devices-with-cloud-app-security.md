---
title: "Control access from unmanaged devices with Cloud App Security"
date: 2020-09-05
tags: 
  - "access"
  - "byod"
  - "cloud-app-security"
  - "data"
  - "mam"
  - "personal"
  - "reverse-proxy"
  - "session-control"
  - "windows10"
image: "/assets/images/pexels-luis-quintero-2339722-scaled.jpg"
---

When COVID-19 hit the world, most people had to work from home. I can imagine that a lot of organizations were not ready for this. So they might have ended up letting their employees working from their personal devices. In some way, this is what we always preached: _anywhere, anytime, and on any device._ But when you enable Bring Your Own Devices for Microsoft 365 services, there is a lot to think of.

Amongst others, the main challenge is to **protect** your user's **identity** and **data**. I'm not going into detail on Identity Protection, but you can take a look at some of my other blogs, [here](https://janbakker.tech/close-the-gap-azure-ad-identity-protection-conditional-access/), [here](https://janbakker.tech/microsoft-secure-score-series-11-turn-on-user-risk-policy/), and [here](https://janbakker.tech/microsoft-secure-score-series-06-enable-policy-to-block-legacy-authentication/).

![](/assets/images/pexels-luis-quintero-2339722-scaled.jpg)

I see a lot of organizations that allow unlimited access from personal devices. That means that users can be really productive, installing Microsoft 365 Apps for enterprise, and syncing SharePoint and Onedrive libraries to their local machines. Without the proper data protection products in place, that is a disaster. Users can copy this data to USB devices, upload them to personal cloud storage services like Dropbox and Google Drive, and can share the documents through personal email accounts.

## Data protection

Data protection is not an easy job, so organizations are focusing on the 'low hanging fruit' first. Data protection is often pushed back on the backlog due to complexity and the lack of time and resources. Because data protection is not an IT responsibility alone. Various stakeholders must be involved, and a large part of implementation is spent on classification and adoption/training of the users.

That brings me to the core of this blog post. When you don't have proper data protection features in place (yet), you might want to control/limit access from unmanaged devices. I think I speak for all of you when I say that you don't want to enroll your personal devices (often shared) into your companies MDM.

## Built-in possibilities

There are some built-in settings that you can configure regarding unmanaged devices. SharePoint for example has the ability to limit or block access from unmanaged devices. When you configure this, a Conditional Access policy will be created. Take note, that this will also have an impact on Teams files.

![](/assets/images/image.png)

## Microsoft Cloud App Security

Today we take a look at Cloud App Security. Using MCAS, you can take control over the session to Office 365 apps. This allows you to control actions (_copy, paste, send & print_) and prevent downloads of documents on unmanaged devices. Let me show you how this works.

First, we'll need to route the application to Cloud App Security using Conditional Access. In this example, we use Office365 and Windows 10, but you can adjust the conditions to your needs.

![](/assets/images/762-05-09-2020-745x1024.png)

**\+ Create a new policy**

**Users and groups**: Select the user. Start with a test user!  
**Cloud apps or actions**: Select **Office 365**  
**Conditions**:  
Select _Device state (Preview)_, All device state, and **_exclude_** Device Hybrid Azure AD joined and Device marked as compliant.  
Select _Device platforms_: **Windows**  
**Session**: Use Conditional Access App Control, Use custom policies

## Session policies

Next, head over to the [Cloud App Security Portal.](https://aka.ms/mcasportal) Now Office 365 apps are redirected to MCAS, we can configure some policies to control the session. Next to that, we block access for desktop apps from unmanaged devices.

First, let's start with the session policy to block all downloads on personal devices. Create a new policy like the example here below.

- ![](/assets/images/766-05-09-2020-1024x1020.png)
    
- ![](/assets/images/767-05-09-2020-1024x796.png)
    

Now, when the users logs in, they get prompted with this message:

![](/assets/images/764-05-09-2020.png)

```
You can change this behaviour in the Settings pane. You're able to customize the message or just disable it. 
```

![](/assets/images/image-8.png)

Next, when the user tries to download a file, this is blocked. An empty dummy file is downloaded instead.

![](/assets/images/765-05-09-2020.png)

In addition to that, the user is notified per email, like we configured in the policy.

![](/assets/images/image-2.png)

## Restrict copy/paste and print

If you like to restrict specific action, you can create another session policy. Use the template '**Block cut/copy and paste based on real-time content inspection'** and adjust the settings like the example below.

- ![](/assets/images/769-05-09-2020.png)
    
- ![](/assets/images/770-05-09-2020.png)
    

Now, when the user is trying to copy or past data, the action is blocked.

![](/assets/images/image-3.png)

## Access policies

As you might have seen while configuring the polices, these policies will only apply to browser sessions. So, you might want to block access to desktop apps for personal devices, or devices that fell out of compliancy.

<figure>

![](/assets/images/763-05-09-2020.png)

<figcaption>

Session control only applies to browsers.

</figcaption>

</figure>

In order to do that, we create an access policy.

![](/assets/images/776-05-09-2020.png)

When the selected user tries to access Teams with the Desktop app, this will not be allowed.

![](/assets/images/image-5.png)

## Alerts

All polices matches can be found in the alerts section.

![](/assets/images/image-6-1024x403.png)

You can configure the alerts to your needs.

![](/assets/images/image-7.png)

With the playbook option, you can trigger Power Automate flows which will extend the capabilities to pretty much anything.

## Mobile apps

One thing I want to point out here. For mobile apps, you don't necessarily have to block the access. Instead, you can use [Intune App Protection](https://docs.microsoft.com/en-us/mem/intune/apps/app-protection-policy) for mobile devices like iOS, iPadOS, and Android. App protection, also known as MAM, can prevent data leakage and can protect the apps with an extra layer of security like a PIN.

Unfortunately, there is no such thing as MAM for Windows (or Mac). There is Windows Information Protection for Windows 10 (WIP-WE), but my personal experience with those implementations are not really good, to be honest. Devices need to be Azure AD registered and WIP-WE is just designed for _accidental_ leakage of data.

## Conslusion

Personal devices can be tough to manage. In this example, I showed you how to ease the pain a little bit. There are a few other ways to get the same thing done, but I like the granularity of the policies in MCAS. On top of that, you can customize the user prompts, so even if the user doesn't know the organization's policy on using personal devices, the user will learn it along the way.

Check out the [Microsoft Cloud App Security Licensing Datasheet](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO) for license related questions. If you don't have the proper license, you can also use Conditional Access to block the desktop apps for unmanaged devices. (Azure AD premium P1 needed). With the built-in controls in SharePoint ant Exchange, you can set the behavior for unmanaged devices. Session control (CASB) is not possible without MCAS.

Check out the documentation if you want to learn more:

[https://docs.microsoft.com/en-us/cloud-app-security/proxy-intro-aad](https://docs.microsoft.com/en-us/cloud-app-security/proxy-intro-aad)  
[https://docs.microsoft.com/en-us/cloud-app-security/session-policy-aad](https://docs.microsoft.com/en-us/cloud-app-security/session-policy-aad )
