---
title: "Enable Location Information and Code Match for Azure MFA"
date: 2021-10-28
categories: 
  - "entra"
  - "security"
image: "/assets/images/IMG_3270bbb.png"
---

##### Update 26-11-2021

As this feature is now in public preview, you can also manage those settings via the Azure portal now. You can find the new settings under **Azure Active Directory -> Security -> Authentication methods -> Authenticator App.**

![](/assets/images/image-6.png)

By default, both settings are [managed by Microsoft](https://docs.microsoft.com/en-us/azure/active-directory/authentication/how-to-mfa-microsoft-managed). You can either enable or disable the feature.

![](/assets/images/image-7.png)

* * *

Learn more from the Microsoft docs:

[Use number matching in multifactor authentication (MFA) notifications (Preview) - Azure Active Directory | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/authentication/how-to-mfa-number-match)  
[Use additional context in multifactor authentication (MFA) notifications (Preview) - Azure Active Directory | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/authentication/how-to-mfa-additional-context)

##### Original article starts here

Today, a quick tip that I'd like to share with you. When scrolling through Twitter, this tweet caught my attention, and I asked [Nathan](https://twitter.com/NathanMcNulty) if I could write this down for everybody to read. So, thanks Nathan for pointing this out. Good catch!

![](/assets/images/image-38.png)

## How to enable this

Now, to start off, this "feature" is not officially supported, so don't use this in your production environment. When enabling this on your tenant, users will be prompted for Code Match with Azure MFA and Phone Sign-in using the Authenticator app. It can only be set using Graph API.

Go to [https://aka.ms/ge](https://aka.ms/ge), make sure you are signed in and have the right permissions to change tenant settings. If not done already, please consent the Policy.ReadWrite.AuthenticationMethod permissions.

![](/assets/images/image-44.png)

Run the following query:

```
GET https://graph.microsoft.com/beta/policies/authenticationMethodsPolicy/authenticationMethodConfigurations/microsoftAuthenticator
```

* * *

Now grab the response, and copy that into the body. Your body might look like this:

```
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#authenticationMethodConfigurations/$entity",
    "@odata.type": "#microsoft.graph.microsoftAuthenticatorAuthenticationMethodConfiguration",
    "id": "MicrosoftAuthenticator",
    "state": "enabled",
    "includeTargets@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authenticationMethodsPolicy/authenticationMethodConfigurations('MicrosoftAuthenticator')/microsoft.graph.microsoftAuthenticatorAuthenticationMethodConfiguration/includeTargets",
    "includeTargets": [
        {
            "targetType": "group",
            "id": "all_users",
            "isRegistrationRequired": false,
            "authenticationMode": "any",
            "outlookMobileAllowedState": "default",
            "displayAppInformationRequiredState": "default",
            "numberMatchingRequiredState": "default"
        }
    ]
}
```

Change the value from numberMatchingRequiredStateand to **enabled**, and select PATCH to update the policy. This will enable Code Match for Azure MFA.

![](/assets/images/image-41.png)

You can also change displayLocationInformationRequiredState to **enabled** as well, to enable Location and App information on the MFA and sign-in prompts.

<figure>

![](/assets/images/IMG_3270.png)

<figcaption>

displayLocationInformationRequiredState enabled

</figcaption>

</figure>

<figure>

![](/assets/images/image-43.png)

<figcaption>

numberMatchingRequiredState enabled

</figcaption>

</figure>

That's it for today. Cool right?
