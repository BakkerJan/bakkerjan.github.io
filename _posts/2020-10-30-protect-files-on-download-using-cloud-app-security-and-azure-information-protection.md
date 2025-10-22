---
title: "Protect files on download using Cloud App Security and Azure Information Protection"
date: 2020-10-30
categories: 
  - "cloud-app-security"
  - "security"
tags: 
  - "azure-information-protection"
  - "byod"
  - "cloud-app-security"
  - "download"
  - "encryption"
  - "protection"
  - "unmanaged-devices"
coverImage: "pexels-artem-beliaikin-912005.jpg"
---

Protect your files on unmanaged devices

Using Azure Information Protection & Microsoft Cloud App Security

If you have read my blog about [Bring Your Own Devices](https://janbakker.tech/food-for-thought-bring-your-own-disaster/), on how important it is to protect your Office 365 files, you might find value in this one too. Today, we take a look at Cloud App Security again. We are going to use the integration with Azure Information Protection.

Our goal today: **Protect** our files in Office 365 when they are downloaded to unmanaged devices. This will prevent unauthorized access to the files when the file is shared with external users, or copied to external media.  
If you're looking for a way to **block** downloads on unmanaged devices, please reach out to [my other blog post](https://janbakker.tech/control-access-from-unmanaged-devices-with-cloud-app-security/).  

## The challenge with Bring Your Own Devices

Some organizations allow their users to work on private devices, like MacBooks, personal Windows computers, or maybe even Linux devices. Office 365 for web has a unified experience, so it does not really matter what device you are using, as long as you use the browser, this will be an overall same experience. Protecting files on the device level is very hard, if not impossible sometimes, so allowing your employees to work with the [web version](https://portal.office.com) of Office 365 makes sense. It gives them the flexibility to use any device they want, and it gives the organization a few more options to protect their files.

Using Microsoft Cloud App Security, we can use the CASB feature to redirect all traffic through a proxy and apply session controls to that session. That means you can control what a user can and can't do. Today, we want to be sure that if the files are downloaded, they are well protected.

## First things first

To get started, you need the right licenses. To enable this feature, you need both a Cloud App Security license and a license for Azure Information Protection Premium P1. As soon as both licenses are in place, Cloud App Security syncs the organization's labels from the Azure Information Protection service. Also, make sure that [Office 365 is connected to Cloud App Security.](https://docs.microsoft.com/en-us/cloud-app-security/connect-office-365-to-microsoft-cloud-app-security)

To use labels in Cloud App Security, the labels must be published as part of the policy. If you're using Azure Information Protection, labels must be published via the Azure Information Protection portal. If you migrated to unified labels, labels must be published via [Office 365 Security and Compliance Center.](https://protection.office.com/)

First, let's create a sensitivity label that will be applied to the file when it is downloaded from Office 365. Cloud App Security retrieves the list of all the classification labels used in your tenant. This action is performed **_every hour_** to keep the list up-to-date.

## Create the label

Head over to the [Office 365 Security Center](https://security.microsoft.com/). Create a new label for files and apply protection to the label. In this example, all employees have Reviewer rights to the document. You can also configure offline access and expiration if needed.

![](/assets/images/1375-30-10-2020-1.png)

## Publish the label

Next, make sure that you publish the label to your users.

![](/assets/images/image-23.png)

## Create the Conditional Access policy

In order to redirect the users to MCAS, we'll need to create a Conditional Access policy. Make sure that you test this first, by selecting only a single or a few test user(s).

My policy is created as follows:

User and groups: **_Allan Deyoung (test user)_**  
Cloud apps or actions: **_Office 365_**  
Conditions ->Device state (Preview): **All device state** _included_**. Device Hybrid Azure AD joined & Device marked as compliant** _excluded_**.**  
Conditions ->Device Platforms: **Windows**  
Session: **Use Conditional Accces App Control -> Use custom policy**

![](/assets/images/image-24.png)

## Create Cloud App Security session policy

Now that all traffic is redirected to Microsoft Cloud App Security, we can create the session policy. Start by creating a new session policy.

![](/assets/images/image-25.png)

Configure the policy to apply the label on download. Make sure that Office 365 is selected as app and your user is also in scope if the policy.

![](/assets/images/1362-30-10-2020.png)

Select the label that we just created before.

![](/assets/images/1363-30-10-2020.png)

Make sure that you enable: **_Block download of any file that is unsupported by native protection or where native protection is unsuccessful._** This will prevent files from downloading when protection cannot be applied. Customize your block message if needed. It's recommended to explain the action to the user.

Cloud App Security currently supports applying Azure Information Protection classification labels for the following file types:

**Word:** docm, docx, dotm, dotx  
**Excel:** xlam, xlsm, xlsx, xltx  
**PowerPoint:** potm, potx, ppsx, ppsm, pptm, pptx  
**PDF**

Instead of applying a label, you can also configure custom permissions that apply to the person that downloads the file.

![](/assets/images/image-34.png)

## End-user experience

Now that everything is in place, let's see how this looks from end end-user perspective. My test user Allan is on an unmanaged device and is working on a project in Onedrive for Business. Allan is prompted that the session is monitored. Also notice that the URL is slightly changed since all the traffic is routed through MCAS.

![](/assets/images/image-26.png)

Allan can work on the documents in the browser without any interruption. When Allan wants to download the document, the label is applied to the document. Keep in mind that the original file remains unprotected in the cloud.

![](/assets/images/image-28.png)

Before Allan can open the document, he is prompted to authenticate. As you can see, the permissions apply to the document.

- ![](/assets/images/image-30.png)
    
- ![](/assets/images/image-31.png)
    

Files that cannot be protected, are blocked from downloading. If configured, the customized message is shown to the user.

![](/assets/images/image-32.png)

## Activity log

You can check the activity log for specific action types, such as blocked or protected. Here you can check whether the files are being protected as planned.

![](/assets/images/image-33.png)

## Close the gap

Session control is only supported in the browser, so desktop and mobile apps could still access and download this data unprotected. We want to block access to apps for unmanaged devices. In this case, we are using an access policy in Cloud App Security. Keep in mind, that you could also do this with Conditional Access.

In Cloud App Security, create a new policy. Make sure that client app is set to **_Mobile and Desktop_**.

![](/assets/images/image-35.png)

When Allan tries to access Teams for example with his desktop app, the access is blocked.

![](/assets/images/1409-30-10-2020.png)

## Let's wrap up

As you can see, Microsoft Cloud App Security is really powerful when it comes to integration with other Microsoft Security products.

I often hear: do you need an E5 license for that? Keep in mind that you can also buy standalone licenses for Microsoft Cloud App Security. For more detailed information, check the [Microsoft Cloud App Security Licensing Datasheet](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)

More information on the AIP and MCAS integration:

[Integrate Azure Information Protection with Cloud App Security | Microsoft Docs](https://docs.microsoft.com/en-us/cloud-app-security/azip-integration)

Stay safe!
