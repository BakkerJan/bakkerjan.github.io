---
title: "Authenticator Lite - Approve Azure MFA prompts with the Outlook app"
date: 2023-03-14
categories: 
  - "entra"
  - "security"
image: "/assets/images/Design-1.png"
---

Microsoft [released](https://www.microsoft.com/en-us/microsoft-365/roadmap?filters=&searchterms=122289) a new feature where the Outlook mobile app now has some of the Microsoft Authenticator App features onboard. Users can now enroll for Azure MFA using just their Outlook mobile app. No additional installation of the Microsoft Authenticator app is needed. This preview brings both push notifications and TOTP to the Outlook mobile app. Users are prompted for enrollment or can manually register their app to work with a Microsoft 365 account once this feature is enabled.

What does that look like for the end user?

## How to enable the feature?

By default, Authenticator Lite is Microsoft managed and disabled during the preview. One month after general availability, the Microsoft managed state default value will change to enabled.

**Update 30-03-2022.** This feature can now be configured using the UX. [How to enable Microsoft Authenticator Lite for Outlook mobile (preview) - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/authentication/how-to-mfa-authenticator-lite#enablement-authenticator-lite-in-azure-portal-ux)

![](/assets/images/image-9.png)

As with most of the preview features, this one is not configurable using the UI. For now, the easiest way to configure this is by using [Graph Explorer](https://aka.ms/ge). First, we'll run a GET request to get the current configuration. In Graph Explorer, you need to consent to the **Policy.ReadWrite.AuthenticationMethod** permission.

```
GET https://graph.microsoft.com/beta/authenticationMethodsPolicy/authenticationMethodConfigurations/MicrosoftAuthenticator
```

The feature is represented in the "_companionAppAllowedState_" section and is disabled by default.

![](/assets/images/image-3.png)

Now copy the response, and edit the companionAppAllowedState to **"enabled"** to enable this feature for all users. When you want to enable the feature for a select group of users, add the groupID instead of _all\_users_ to the _includetarget_ section.

```
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#authenticationMethodConfigurations/$entity",
    "@odata.type": "#microsoft.graph.microsoftAuthenticatorAuthenticationMethodConfiguration",
    "id": "MicrosoftAuthenticator",
    "state": "enabled",
    "isSoftwareOathEnabled": false,
    "excludeTargets": [],
    "featureSettings": {
        "companionAppAllowedState": {
            "state": "enabled",
            "includeTarget": {
                "targetType": "group",
                "id": "all_users"
            },
            "excludeTarget": {
                "targetType": "group",
                "id": "00000000-0000-0000-0000-000000000000"
            }
        },
        "numberMatchingRequiredState": {
            "state": "enabled",
            "includeTarget": {
                "targetType": "group",
                "id": "all_users"
            },
            "excludeTarget": {
                "targetType": "group",
                "id": "00000000-0000-0000-0000-000000000000"
            }
        },
        "displayAppInformationRequiredState": {
            "state": "enabled",
            "includeTarget": {
                "targetType": "group",
                "id": "all_users"
            },
            "excludeTarget": {
                "targetType": "group",
                "id": "00000000-0000-0000-0000-000000000000"
            }
        },
        "displayLocationInformationRequiredState": {
            "state": "enabled",
            "includeTarget": {
                "targetType": "group",
                "id": "all_users"
            },
            "excludeTarget": {
                "targetType": "group",
                "id": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    "includeTargets@odata.context": "https://graph.microsoft.com/beta/$metadata#authenticationMethodsPolicy/authenticationMethodConfigurations('MicrosoftAuthenticator')/microsoft.graph.microsoftAuthenticatorAuthenticationMethodConfiguration/includeTargets",
    "includeTargets": [
        {
            "targetType": "group",
            "id": "all_users",
            "isRegistrationRequired": false,
            "authenticationMode": "any"
        }
    ]
}
```

Copy the payload to the Request body section, and change the method to **_PATCH_**. Be careful, as this will change the current settings for your Authenticator app. A mistake is easily made at this point.

![](/assets/images/image-4.png)

After running the PATCH, check if the settings are updated by running the same command with the GET method.

![](/assets/images/image-5.png)

## User enrollment

As soon as the feature is enabled, the included users might be prompted to register in the Outlook app. During registration, the user might be prompted for step-up authentication. It is also possible to register manually.

This video shows the manual registration.

1. In the Outlook app, go to the settings icon

3. Pick the Office 365 account that is eligible for this feature

5. Scroll down and select "_**Authenticator**_" (when this section is not shown, your app is not updated yet)

7. Switch the toggle "**Approve sign-ins**" to start the registration process.

9. The user can be prompted for step-up authentication, for example, via SMS or Temporary Access Pass

11. When step-up authentication is successful, the feature is enabled.

You can track these events in the audit logs.

![](/assets/images/image-6.png)

In the sign-in logs, under _authenticationAppDeivceDetails_, the _clientApp_ field will return _**microsoftAuthenticator**_ or _**Outlook**_.

![](/assets/images/image-7.png)

As this feature is brand new, expect some more details to be added to this post over the coming weeks. For now, please note these requirements and current limitations.

- **Outlook app version**. The new app is currently rolling out. It can take some time before the app is updated. For Android, the version must be _4.2308.0_ or higher. For iOS, the app version needs to be _4.2309.0_ or higher.

- **Authenticator app policy**. Users need to be enabled for the Microsoft Authenticator app and the Authentication mode set to “_Any_” or “_Push_.”

- **Additional Context and Number match**. Number match will be enabled for all users. Additional Context is not supported.

- **Chicken - egg**. Users need at least one authentication method registered to their account, for example, a phone number or Temporary Access Pass.

- **Support**. This feature is not supported for Azure MFA server (legacy). It is supported in both hybrid and cloud-only scenarios but only works with Azure MFA.

- **ADFS**. If your organization uses ADFS adapter or NPS extensions, upgrade to the latest versions for a consistent experience.

- **Shared device**. Users enabled for shared device mode on Outlook are not eligible for Authenticator Lite

Good to know:

- You **cannot** configure the push notifications, as these are independent of the Authenticator feature settings. Number match will be enabled, and Location/App context will be disabled.

- The registration will still appear as "Microsoft Authenticator" in the Azure and My Sign-ins portal.

- When both the Authenticator app and Outlook mobile app are registered on one device, the prompt will be sent to the Authenticator app only.

- When prompted for enrollment, and users click out of the registration or do not complete the authentication will be repromoted one time after seven days.

- When the user disables the feature, the Authenticator app registration will also be deleted in Azure AD.

- Authenticator Lite can't be used for SSPR.

Here is the official Microsoft documentation: [How to enable Microsoft Authenticator Lite for Outlook mobile (preview) - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/authentication/how-to-mfa-authenticator-lite)

Stay safe!
