---
title: "Get alerts on Azure resource assignments made outside PIM"
date: 2022-06-30
categories: 
  - "entra"
  - "security"
coverImage: "pexels-lucas-fonseca-2239655-scaled.jpg"
---

Microsoft released [a new public preview](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/whats-new#public-preview---roles-are-being-assigned-outside-of-privileged-identity-management) where admins can be alerted when assignments to Azure resources are made outside of Privileged Identity Management.

This was already possible in combination with Azure AD roles, but the new preview now applies to Azure resources as well. Where alerts on Azure AD roles are enabled by default, alerts for Azure resources need to be enabled by an administrator first.

This feature is extremely valuable when you want to govern your Privileged Access implementation, to make sure given access is always time-bound and least privileged. By enabling this alert, you can stay informed when a role is managed directly through the Azure IAM resource blade or the Azure Resource Manager API, instead of PIM.

## How to enable it?

You can enable the alert by going into the Azure AD Privileged Identity portal. Select **_Azure resources_** from the left side panel, and choose the subscription that you want to be alerted on\*.

```
*During the public preview of the Roles are being assigned outside of Privileged Identity Management (Preview) alert, Microsoft supports only permissions that are assigned at the subscription level.
```

![](/assets/images/image.png)

Next, select **_Alerts_**, and select **_Settings_**.

![](/assets/images/image-1.png)

Select the new alert, and click **_Enable_**. Confirm to enable the alert.

![](/assets/images/image-2.png)

## Let's put it to the test

Now, when roles are added outside of PIM, an alert is sent out via email. You can also see the alert being raised in the Azure portal.

![](/assets/images/image-3.png)

![](/assets/images/msedge_G0u8pOD3pw.png)

When the alert is raised, you can fix the issue straight from the PIM portal. Select the alert, and click **Fix**.

PIM will then delete the role from the resource.

![](/assets/images/image-4.png)

This is also visible in the Activity log.

![](/assets/images/image-6.png)

## Wrap things up

As you can see, this feature is quite powerful. I'm sure this will help Privileged Identity admins to keep an eye on the resources, and act where necessary. Azure AD Privileged Identity Management is becoming better and better by the day!

Learn more: [What's new? Release notes - Azure Active Directory - Microsoft Entra | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/whats-new#public-preview---roles-are-being-assigned-outside-of-privileged-identity-management)  
[Configure security alerts for Azure roles in Privileged Identity Management - Azure Active Directory - Microsoft Entra | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/privileged-identity-management/pim-resource-roles-configure-alerts#alerts)
