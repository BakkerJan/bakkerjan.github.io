---
title: "Sure, keep me signed in! And don't prompt for MFA!"
date: 2020-05-22
categories: 
  - "entra"
  - "security"
tags: 
  - "cookies"
  - "frequency"
  - "kmsi"
  - "mfa"
  - "persistent"
  - "prompt"
image: "/assests/images/woman-wearing-purple-shirt-holding-smartphone-white-sitting-826349-scaled.jpg"
---

Today a short blog about MFA prompts, session lifetime, and cookies. This will give you an idea of how you can tune the end-user experience and where to configure these settings.

Session lifetime in Azure AD is often mistaken. When you start working with Azure AD, Conditional Access, and Multi-factor authentication, there are a couple of things you should know. The Azure AD defaults are pretty loose. When you leave every setting to default, the user experience is pretty good. Once you logged in to Office 365, your session can be re-used for **90 days**. During that time, you are not prompted for your password, assuming that is it not changed over time.

When organizations deploy MFA, there is one question that always comes back: "how often should we prompt our users for MFA?" These questions are mostly based on gut feeling. Prompting your users for credentials or MFA more often does not mean that you are more secure. When users are used to entering credentials as a routine, they are more like going to fall for phishing attacks. So, think twice when you consider tuning these settings.

#### Keep me signed in (KMSI)

This setting is not easy to find but has a major impact on the user experience. You can configure this setting in the company branding section under **Azure Active Directory -> Company Branding**

![](/assets/images/image-76.png)

The Azure AD sign-in flow gives users the option to remain signed in until they explicitly sign out. This doesn't change Azure AD session lifetime but allows sessions to remain active when users close and reopen their browser. This will create a persistent cookie on the endpoint, so the users' session is stored. The Azure AD default for browser session persistence allows users on personal devices to choose whether to persist the session by showing a “Stay signed in?” prompt after successful authentication.

![](/assets/images/image-80.png)

#### Multi-factor Authentication

When you prompt your user for MFA, there is another setting that is coming into the picture. This setting is also not that easy to find. It is stored in the [MFA service settings](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx). The remember Multi-Factor Authentication feature sets a persistent cookie on the browser when a user selects the Don't ask again for X days option at sign-in. The user isn't prompted again for Multi-Factor Authentication from that same browser until the cookie expires. If the user opens a different browser on the same device or clears their cookies, they're prompted again to verify.

![](/assets/images/image-77.png)

This setting is enabled by default. The user experience will look like this:

![](/assets/images/image-79.png)

#### Persistent browser session

Using Conditional Access you can configure whether a session needs to be persistent or not. This will override the setting in Company branding. Using this setting you can make different policies for different scenarios. You can distinguish between users or managed and non-managed devices for example. This will only work correctly when you enable this for all cloud apps.

![](/assets/images/image-82.png)

#### Sign-in frequency

You can manage the frequency of sign-in for Azure AD. Sign-in frequency setting works with apps that have implemented OAUTH2 or OIDC protocols according to the standards. Most Microsoft native apps for Windows, Mac, and Mobile including the following web applications comply with the setting.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- O365 Admin portal
- Exchange Online
- SharePoint and OneDrive
- Teams web client
- Dynamics CRM Online
- Azure portal

If you have configured different Sign-in frequency for different web apps that are running in the same browser session, the strictest policy will be applied to both apps because all apps running in the same browser session share a single session token.

![](/assets/images/image-83.png)

#### What about FIDO2?

When users sign in using FIDO2 security keys, they will not get prompted for MFA and/or the option to stay signed in. FIDO2 stands for strong authentication on itself, so it will satisfy the second-factor authentication as well. All the other settings will apply, such as sign-in frequency and browser persistency.

#### Under the hood

If you want to see what's going on under the hood, you can use [Fiddler](https://www.telerik.com/fiddler) and the developer tools in your browser.

![](/assets/images/image-84.png)

![](/assets/images/image-86.png)

#### Conclusion

As you can see there are quite some settings that allow you to tune the end-user experience for session lifetime and MFA requests. I hope these tips can help you in designing your Azure AD strategy. Hopefully you'll find the balance between the best user experience and the best security measurements. Keep in mind that you should not design this based on gut feeling. Try to bother the hackers, not your users.

Stay safe!
