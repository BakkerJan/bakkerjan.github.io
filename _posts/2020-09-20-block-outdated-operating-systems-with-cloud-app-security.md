---
title: "Block outdated operating systems with Cloud App Security"
date: 2020-09-20
categories: 
  - "entra"
  - "security"
coverImage: "pexels-pixabay-257881-scaled.jpg"
---

It is not unlikely that some of your users still use Windows 7 on their home computers. Or any other outdated operating system (heaven forbid). Despite the warnings, news articles, and endless coffee chit chats about this subject, they still have the _\- "if it ain't broke, don't fix it"_ - mindset, and will eventually use it to access work resources as well.

With the use of Conditional Access you can block specific operating systems, but you cannot specify a specific version, like Windows XP or Windows 7. This is possible with Intune Compliance policies, but they will not apply to unmanaged clients, the scope of today's blog post.

![](/assets/images/image-17.png)

## Bring in Cloud App Security

First, we’ll need to route the application to Cloud App Security using Conditional Access. In this example, I use Office365 as the selected app, and Windows 10 as the selected device platform, but you can adjust this to your own situation.

![](/assets/images/762-05-09-2020-745x1024.png)

**\+ Create a new policy**

**Users and groups**: Select the user. Start with a test user!  
**Cloud apps or actions**: Select **Office 365**  
**Conditions**:  
Select _Device state (Preview)_, All device state, and **_exclude_** Device Hybrid Azure AD joined and Device marked as compliant.  
Select _Device platforms_: **Windows**  
**Session**: Use Conditional Access App Control, Use custom policies

With this action we route all traffic, coming from unmanaged devices, to Cloud App Security.

## Access policy

Next, create an access policy in Cloud App Security and define the policy like the example below.

![](/assets/images/image-19.png)

This is the beauty of Cloud App Security. To test this out, you can only apply the policy to one user and/or app. In this case, we select **User agent tag** -> equals -> **Outdated operating system**.

Take note: I could not find any official documentation in this, but I read a post on the tech community website that states the following: **_outdated = current version -_2**.

If you want to be more specific, you can also block the exact version based on the user agent string.

<figure>

![](/assets/images/image-20.png)

<figcaption>

User agent string -> contains -> Windows NT 6.1

</figcaption>

</figure>

## User experience

When a user with an outdated operating system tries to access one of the resources, the session is blocked.

![](/assets/images/image-22.png)

In the Cloud App Security portal, an alert is created.

![](/assets/images/image-21.png)

## Wrap up

One thing I have to point out here: **user agents can be easily spoofed**. This is not something Microsoft or Cloud App security can change for you, but it's important to keep in mind when you design your Conditional Access and MCAS policies. Do not just configure policies for the clients that you support, but also create a safety net for the clients that are not specified or not supported. Do not just focus on the most common devices and browsers.

<figure>

![](/assets/images/image-23.png)

<figcaption>

User agents can be spoofed very easy

</figcaption>

</figure>

In this blog post, I showed you the powerful capabilities of Cloud App Security. Please check out my [other blogs](https://janbakker.tech/tag/cloud-app-security/) about this product.

Check out the Microsoft documentation if you want to learn more:

[](https://docs.microsoft.com/en-us/cloud-app-security/proxy-intro-aad)[Protect with Microsoft Cloud App Security Conditional Access App Control | Microsoft Docs](https://docs.microsoft.com/en-us/cloud-app-security/proxy-intro-aad)  
[](https://docs.microsoft.com/en-us/cloud-app-security/access-policy-aad)[Create Cloud App Security access policies to allow and block access | Microsoft Docs](https://docs.microsoft.com/en-us/cloud-app-security/access-policy-aad)

Stay safe!
