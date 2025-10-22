---
title: "System-preferred multifactor authentication in Azure AD. Don't settle for less."
date: 2023-03-03
categories: 
  - "entra"
  - "security"
coverImage: "pexels-ketut-subiyanto-4247721-scaled.jpg"
---

A new feature has popped up in Azure AD: **System-preferred multifactor authentication (MFA)**. This will allow administrators to enforce the most secure method for Azure MFA. For example, if a user has multiple methods registered, the most secure method will be prompted first. How do I know what method is the strongest, you may ask? Here is the current order from most to least secure methods, currently supported in Azure Active Directory:

1. Temporary Access Pass

3. Certificate-based authentication

5. FIDO2 security key

7. Microsoft Authenticator notification

9. Companion app notification

11. Microsoft Authenticator time-based one-time password (TOTP)

13. Companion app TOTP

15. Hardware token based TOTP

17. Software token based TOTP

19. SMS over mobile

21. OnewayVoiceMobileOTP

23. OnewayVoiceAlternateMobileOTP

25. OnewayVoiceOfficeOTP

27. TwowayVoiceMobile

29. TwowayVoiceAlternateMobile

31. TwowayVoiceOffice

33. TwowaySMSOverMobile

This list is dynamic and may change as new methods are introduced or old ones disappear. Always check the current docs for the current status: [System-preferred multifactor authentication (MFA) - Azure Active Directory - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/authentication/concept-system-preferred-multifactor-authentication#how-does-system-preferred-mfa-determine-the-most-secure-method)

## How to configure this setting?

**_Update 27-02-2023_**. This setting can now be configured using the Azure portal as well.

![](/assets/images/image-8.png)

At the time of writing, this setting can only be set using Graph API. The super duper [Graph Explorer tool](https://aka.ms/ge) is the easiest way to do this.

First, make sure you are signed in with proper permissions that allow you to change this setting. In Graph Explorer, you need to consent to the **_Policy.ReadWrite.AuthenticationMethod_** permission. Run the following query to get the current state of the setting. We are looking for the _systemCredentialPreferences_ setting.

```
GET https://graph.microsoft.com/beta/policies/authenticationMethodsPolicy
```

![](/assets/images/image.png)

By default, system-preferred MFA is [Microsoft managed](https://learn.microsoft.com/en-us/azure/active-directory/authentication/concept-authentication-default-enablement#microsoft-managed-settings) and disabled during the preview. After general availability, the Microsoft managed state default value will change to enable system-preferred MFA.

To enable the feature for all users, run:

```
PATCH https://graph.microsoft.com/beta/authenticationMethodsPolicy
Content-Type: application/json

{
    "systemCredentialPreferences": {
        "state": "enabled",
        "includeTargets": [
            {
                "id": "all_users",
                "targetType": "group"
            }
        ]
    }
}
```

![](/assets/images/image-1.png)

You can also include or exclude (dynamic) groups for this feature. Just reach out to the guidance here:

[System-preferred multifactor authentication (MFA) - Azure Active Directory - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/authentication/concept-system-preferred-multifactor-authentication#authentication-method-feature-configuration-properties)

Example:

```
PATCH https://graph.microsoft.com/beta/policies/authenticationMethodsPolicy
Content-Type: application/json

{
    "systemCredentialPreferences": {
        "state": "enabled",
        "excludeTargets": [
            {
                "id": "d1411007-6fcf-4b4c-8d70-1da1857ed33c",
                "targetType": "group"
            }
        ],
        "includeTargets": [
            {
                "id": "all_users",
                "targetType": "group"
            }
        ]
    }
}
```

## User experience

So, how does this looks on the users' end? In this example, the user has registered three methods:

1. FIDO2 key.

3. Microsoft Authenticator App.

5. SMS.

When the user signs in, only FIDO2 is prompted. The user can still cancel out and pick another method if needed.

As a result, users can no longer set their default method, as the most advisable sign-in method is used. Optinally, a user can change their fall-back method, in case the first method is unavailable.

<figure>

![](/assets/images/image-2.png)

<figcaption>

You're using the most advisable sign-in method where it applies.

</figcaption>

</figure>

## Let's wrap up

This is a really nice feature, as many users (and admins) still can't find their way into the authentication methods maze. With this feature, admins can make sure that users are using the strongest method possible while still giving users the ability to change the method during sign-in. Together with the Registration Campaign (Nudge) feature, this will improve the overall security posture for many companies.

Read more: [System-preferred multifactor authentication (MFA) - Azure Active Directory - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/authentication/concept-system-preferred-multifactor-authentication)

Stay safe!
