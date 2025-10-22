---
title: "Get started with web content filtering in MDATP"
date: 2020-02-23
categories: 
  - "security"
tags: 
  - "defender"
  - "intune"
  - "mdatp"
  - "network-protection"
  - "web-content-filtering"
  - "windows-10"
coverImage: "msedge_3hIwil5Tb4.png"
---

**Update 7-7-2020: [Microsoft anounced](https://techcommunity.microsoft.com/t5/microsoft-defender-atp/an-update-on-web-content-filtering/ba-p/1505445#.XwNfwYSQZOc.linkedin) that you no longer need a Cyren subscription.** Web content filtering will be offered as part of Microsoft Defender ATP without any additional partner licensing. Now you get the benefits of web content filtering without the need for additional agents, hardware, and costs.  

From the article:

If you joined in on the public preview, you might be in one of the following scenarios: 

- If your 60-day trial for the partner license has already expired, all your policies are now active and protecting your enterprise.  
- If you have an active 60-day trial for a partner license, all your policies will continue to work even after 60 days.  

You can un-register any partner integration that you have previously signed up for in the Azure portal: 

- Go to Azure Active Directory > [App Registrations](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) 
- Search for the name you have registered the partner app (Cyren)  
- Select the partner application and delete it. 

Web content filtering is part of Web protection in Microsoft Defender ATP. This gives you the ability to audit or even block websites based on a specific category.

* * *

## Network protection

Web content filter uses Network protection to cover 3rd party browsers and uses Smartscreen to protect Edge. Before you start with, be sure that Network protection is enabled. You can do this, using the [ATP baseline](https://docs.microsoft.com/en-us/intune/protect/security-baseline-settings-defender-atp) settings in Intune or trough [GPO or registry settings](https://docs.microsoft.com/en-us/windows/security/threat-protection/microsoft-defender-atp/enable-network-protection). If you want to enable Network protection manually on a device, the easiest way is to do it with Powershell.

```
Set-MpPreference -EnableNetworkProtection Enabled
```

## Machine Groups

Next, you want to be sure that you have at least one machine group created, with test devices included. Otherwise, the policy will have effect on all of your devices that you have onboarded with ATP.

## Other prerequisites

Before you can try out this feature you'll need a couple of things in place:

- Windows 10 Enterprise E5 license (or [trial](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp?ocid=docs-wdatp-main-abovefoldlink&rtc=1))
- You need access to the Microsoft Defender Security Center portal in order to enable this feature and configure the policies
- Devices running Windows 10 Anniversary Update (version 1607) or later with the latest Defender update (for Network Protection on Internet Explorer, Edge, Chrome, or Firefox)
- Devices must run Windows 10 May 2019 (1903) or later. (for better user experience from SmartScreen on Edge).
- A Cyren (trial) license

## Let's get started

To enable web content filtering, you'll need to sign up for a trial with Cyren. In your Microsoft Defender Security Center portal, go to **Reports > Web protection** from the side navigation. Select the **Connect to a partner** button and follow the instructions in the wizard.

You'll be redirected to the Cyren setup page. Follow the steps to enable the trial and accept the consent pop-up.

<figure>

![](/assets/images/image-10.png)

<figcaption>

Cyren is offering a 60-day free trial for all Microsoft Defender ATP customers

</figcaption>

</figure>

Cyren needs some permissions in order to read your tenant info from your Microsoft Defender ATP account, such as your tenant ID, which will be tied to your Cyren license. [Learn more about this consent.](https://docs.microsoft.com/en-us/windows/security/threat-protection/microsoft-defender-atp/web-content-filtering#cyren-permissions)

![](/assets/images/image-11.png)

Next, you can continue and enable Web content filtering. Go to **Settings** > **Advanced features**.

![](/assets/images/msedge_g5dEjEVu1q.png)

After we enabled this feature we can now continue and make our first policy. Navigate to **Settings** \> **Web content filtering** and choose for **\+ Add policy**

![](/assets/images/msedge_36ALa9vIGN.png)

Enter a name for your policy, and choose **Next**. In this example, I like to test out the Social Media category. Expand the **Leisure** section and select **Social networking**. Next, choose the right scope for this policy. Select the machine group(s) and choose **Next**.

- ![](/assets/images/image-12-738x1024.png)
    
- ![](/assets/images/msedge_jbxZBkqkiB-738x1024.png)
    
- ![](/assets/images/msedge_JeADEbM5do-738x1024.png)
    

Finish the wizard to apply the policy. Is takes around 15 minutes before the policy takes effect.

## End-user experience

Next, move over to your test device and see if you can still access the blocked websites. If you want to do a category check you can use this URL: [https://www.cyren.com/security-center/url-category-check](https://www.cyren.com/security-center/url-category-check)

![](/assets/images/image-13.png)

For the best user experience use Edge. The action is blocked by SmartScreen. If SmartScreen is turned off, it will fall back to Network protection.

<figure>

![](/assets/images/mstsc_F2AuGEGU68.png)

<figcaption>

End-user experience using Edge browser

</figcaption>

</figure>

In other browsers, Network protection blocks the action and the browsers will display an error page. A toast notification is shown to the user.

<figure>

- ![](/assets/images/mstsc_fQoZBusag3-1024x524.png)
    
- ![](/assets/images/mstsc_ir9cqvMPOT-1024x532.png)
    
- ![](/assets/images/mstsc_e7KschVqdA-1024x455.png)
    
- ![](/assets/images/mstsc_QVUx4TiZI5-cropped.png)
    

<figcaption>

Network protection will kick in if you use different browsers.

</figcaption>

</figure>

## Reports and logging

![](/assets/images/image-15-575x1024.png)

Administrators can see reports on web content using the Web protection dashboard. Go to **Reports** > **Web protection** to see an overview.

You can look deeper and apply filters if needed. It can take a while for data to show up.

![](/assets/images/image-16.png)

If you want to see what's going on on device level, check the **Timeline** tab on the machine page and filter for Smart Screen events.

![](/assets/images/image-17.png)

#### Create exclusions / whitelisting

Most organizations need some flexibility and might want to allow some websites, even when they are categorized in certain blocked categories. You can allow specific websites by using the Indicators feature. First, make sure that you enabled custom network indicators in the settings pane.

![](/assets/images/561-09-08-2020.png)

After that, you van add URL's in the indicator section. For example, you can allow Facebook, while the social media category remains blocked.

![](/assets/images/image-7.png)

## Conclusion

Using web content filtering trough ATP gives you the ability to regulate access to websites based on their content categories. Many of these websites, while not malicious, might be problematic due to compliance regulations, bandwidth usage, or other concerns. While this feature will getting better in the future, organizations can move away from 3rd party web content filtering and can fully integrate this with Defender ATP.

While testing this feature, it is good to keep notice of a few things:

- Users can still access blocked websites by using proxy websites. According to Cyren proxy websites are categorized as Anonymizers, but this category is not available in ATP.
- I tested Firefox, Google Chrome, Edge, and IE. They all blocked the websites properly.
- You cannot whitelist any website.
- You can [report misclassified URLs](https://www.cyren.com/security-center/url-category-check) to Cyren directly.
