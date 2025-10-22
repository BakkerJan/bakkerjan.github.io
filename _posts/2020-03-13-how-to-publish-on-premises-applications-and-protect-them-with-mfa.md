---
title: "How to publish on-premises applications and protect them with MFA"
date: 2020-03-13
categories: 
  - "entra"
  - "security"
tags: 
  - "application-proxy"
  - "azure-ad"
  - "mfa"
  - "remote"
  - "web-application"
image: "/assets/images/msedge_cFu0KscQNA.png"
---

Using Azure Application Proxy you can publish your on-premises web applications in a secure way. Combining this with Conditional Access, you can configure MFA for example. Now Coronavirus is hitting us hard, you might have to take a look at this feature.

Assume the following use case: you have Citrix or RDS available for 50% of your users, so they can work from home or elsewhere. Now, because of the Coronavirus (or any future disaster), all of your employees have to work from home, and you don't have the resources or licenses to support this.

Of course, Microsoft Teams can help out here. But what about that web application? Azure Application Proxy to the rescue! It is really easy to set up, it does not require any DMZ or complex firewall configurations and you can secure the application with MFA if needed. Not convinced yet? I'll show you right away.

```
 To use Azure AD Application Proxy, you must have an Azure AD Premium P1 or P2 license. 
```

**_For the purpose of this demo, I have quickly installed a dummy web application in IIS._**

Suppose you have a business web application named **Company App01**. It runs in your on-premises datacenter. It is accessible from the internal network only by using the URL: **http://localhost:8080** You want to publish this app to your users in order to use it from home or on the road for example.

The first thing we have to do is installing a small agent on one of your on-premises servers. This can be the application server itself, or another server that can access this web application. First, head over to [https://portal.azure.com](https://portal.azure.com) Go to **Azure Active Directory -> Enterprise Applications -> Application proxy and choose + Configure an app**

![](/assets/images/msedge_fW4k13Ah0J.png)

Next, click on Download Application Proxy Connector and choose Accept & Download to download the package. Now, copy this installer to your preferred server. You can read the requirements in the screenshot below. Keep in mind that this server will be the bridge to your application. This server needs to be able to connect to your application. You can install multiple agents if needed, but for this demo, we keep it at simple as possible.

![](/assets/images/msedge_pfNYOZib8N.png)

The installation of the agent is pretty straight forward. Double click on the installer and accept the agreement. Start the installation by clicking Next. The installer asks to authenticate to your Azure AD. Use your administrator credentials to authenticate to finish the installation.

```
Connector installation requires local admin rights to the Windows server that it's being installed on. It also requires a minimum of an Application Administrator role to authenticate and register the connector instance to your Azure AD tenant. 
```

- ![](/assets/images/mstsc_OCDMWQUADM-1024x752.png)
    
- ![](/assets/images/mstsc_S2KTewceSI-1024x965.png)
    

After you installed the agent, go back into your Azure portal session. If you installed the agent correctly, you can now see this agent live under the default group. Now we can enable the application proxy. If you have multiple connectors, you can make different groups. For now, we leave it like this.

![](/assets/images/msedge_PnTmExrGcL.png)

Next, we are going to configure our application. Click on the **\+ Configure an app** once again. The wizard will start. Now, we fill in all the settings.

![](/assets/images/msedge_6MqdlGvyjC-1009x1024.png)

1. Enter a name for your application.
2. Enter the internal URL. Be sure that your on-premises connector can access this URL.
3. Enter the prefix for the application external URL.
4. Click + Add to create the application.

You can leave the other settings as is, depending on your needs. When you want to use your own domain, instead of the default msappproxy.net domain, additional configuration is needed in order to make this work.

Depending on your application, you can configure [additional settings](https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#add-an-on-premises-app-to-azure-ad). For more information, take a look a the [Microsoft docs.](https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/what-is-application-proxy)

Now our application is created, we need to make this available to our user. But first, you can choose to change the default logo and configure the default user assignment settings. If you leave this default, you need to assign the application to specific users. You can select this from the application **Overview** page. Configure the logo and default settings on the **Properties** page.

![](/assets/images/msedge_cFu0KscQNA.png)

![](/assets/images/msedge_jUpT0Z6p3d.png)

Now, we can integrate this application with Conditional Access to enforce MFA for example. In order to do this, go to the Conditional Acces page and configure the settings to your needs. When you access the Conditional Access policy trough the application itself, the app is already selected.

- ![](/assets/images/msedge_BsvxKuIErC-1024x744.png)
    
- ![](/assets/images/msedge_pxsIOjOnQg-994x1024.png)
    

#### End-user experience

Users can access this application by going to [https://myapps.microsoft.com](https://myapps.microsoft.com) and log in with their Azure/Office 365 credentials. The app will show up if the users are assigned to the application.

![](/assets/images/msedge_pQvdysv4lY.png)

The application is accessible from outside of your company, without the need of having any inbound port open. By using Conditional Access, the application is also protected with MFA.

![](/assets/images/msedge_6xt9uYompN.png)

#### Conclusion

Many apps can be published using the Azure Application Proxy.

![AzureAD Application Proxy diagram](/assets/images/azureappproxxy.png)

Application Proxy works with:

- Web applications that use [Integrated Windows Authentication](https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd) for authentication
- Web applications that use form-based or [header-based](https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-ping-access) access
- Web APIs that you want to expose to rich applications on different devices
- Applications hosted behind a [Remote Desktop Gateway](https://docs.microsoft.com/en-us/azure/active-directory/manage-apps/application-proxy-integrate-with-remote-desktop-services)
- Rich client apps that are integrated with the Active Directory Authentication Library (ADAL)
