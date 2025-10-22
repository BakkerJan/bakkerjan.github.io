---
title: "Manage user-preferred multi-factor authentication method in Microsoft Entra ID"
date: 2023-07-11
categories: 
  - "entra"
  - "security"
image: "/assets/images/image-6.png"
---

This post is all about setting the preferred multi-factor authentication method using Graph API. We already know the [system-preferred multi-factor authentication method](https://janbakker.tech/system-preferred-multifactor-authentication-in-azure-ad-dont-settle-for-less/), where Microsoft Entra ID will use the strongest method of all the registered methods, but this time we take a look a the default method set by the user.

At the time of writing, the default method can only be set by using the new [authentication sign-in preferences](https://learn.microsoft.com/en-us/graph/api/resources/authentication?view=graph-rest-beta) in Graph API; however, some of the API's are already integrated into the Entra portal.

![](/assets/images/image.png)

Update 02-08-2023: You can now change the default sign-in method from the Entra portal as well.

![](/assets/images/image.png)

This information can also be retrieved using the Graph API directly. For the purpose of this demo, we use [Graph Explorer](https://aka.ms/ge).

```
GET https://graph.microsoft.com/beta/users/MeganB@M365x80658054.OnMicrosoft.com/authentication/signInPreferences
```

Ensure your account has the [correct permissions](https://learn.microsoft.com/en-us/graph/api/authentication-get?view=graph-rest-beta&tabs=http#permissions) to retrieve the data.

![](/assets/images/image-1.png)

The response will have three values:

| **isSystemPreferredAuthenticationMethodEnabled** | _Indicates whether system-preferred method is enabled for this user._ |
| --- | --- |
| **userPreferredMethodForSecondaryAuthentication** | _The default second-factor method used by the user when signing in._ |
| **systemPreferredAuthenticationMethod** | _If system-preferred method is enabled, it will show the default/strongest method._ |

Now let's see what the data looks like when a user is enabled for system-preferred authentication method.

![](/assets/images/image-2.png)

As you can see, the user-preferred method is still '_sms_' but is overruled by the system-preferred method, which is now '_Fido2_'.

Let's see if we can change the user-preferred method.

```
PATCH https://graph.microsoft.com/beta/users/MeganB@M365x80658054.OnMicrosoft.com/authentication/signInPreferences

Request body:

{
    "userPreferredMethodForSecondaryAuthentication": "oath"
}
```

Possible values are push, oath, voiceMobile, voiceAlternateMobile, voiceOffice, sms, and unknownFutureValue

![](/assets/images/image-4.png)

Now, if we check what the new values are, we can see the default method is changed to '_oath_'.

![](/assets/images/image-5.png)

## There's an app for that!

Looking at the new API's and seeing that UX controls are still missing from the Entra portal, I decided to build a Powerapp around this API. The concept is pretty simple: PowerApps works as a front-end and is calling two Power Automate flows, one for getting the current settings and another one for updating the settings.

![](/assets/images/PowerAppsFlow.png)

The PowerApp looks like this:

![](/assets/images/image-6.png)

The app is also aware of the methods registered for/by the user, so it will greyed-out the methods that are not available or already set as the default method. All this info is gathered using Power Automate and various API calls.

```
{
  "userpreferredmethodforsecondaryauthentication": "sms",
  "systempreferredauthenticationmethod": "Sms",
  "issystempreferredauthenticationmethodenabled": true,
  "push_registered": false,
  "oath_registered": false,
  "voicemobile_registered": true,
  "voicealternatemobile_registered": false,
  "voiceoffice_registered": false,
  "sms_registered": true
}
```

I've uploaded the PowerApp sample to [Github](https://github.com/BakkerJan/PowerApps/blob/main/Preferredmulti-factorauthenticationmanager_20230711184343.zip) for you to download. Do know that this is far from production ready and is only to be used for educational purposes or as proof of concept.

[![](/assets/images/Octicons-mark-github.svg-1024x1024.png)](https://github.com/BakkerJan/PowerApps/blob/main/Preferredmulti-factorauthenticationmanager_20230711184343.zip)

## Wrap things up

Having an API to set the default multi-factor authentication method can be handy during some projects. Wrapping it inside a PowerApp is even more fun, and I had a blast building this. The concept works with every API endpoint, so I hope this post might inspire you to build your own fancy PowerApp someday. Take a look at the sample from Github, and don't hesitate to ask questions. I'm more than happy to help you out on your adventure.

Here you can find more information about the new API: [authentication resource type - Microsoft Graph beta | Microsoft Learn](https://learn.microsoft.com/en-us/graph/api/resources/authentication?view=graph-rest-beta)

Stay safe!
