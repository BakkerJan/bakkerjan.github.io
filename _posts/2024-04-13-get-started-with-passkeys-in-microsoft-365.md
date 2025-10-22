---
title: "Get started with passkeys in Microsoft 365"
date: 2024-04-13
categories: 
  - "entra"
  - "security"
image: "/assests/images/e229b288-4985-4b1d-a80d-82f30426ec93.jpeg"
---

It's here! A long-awaited feature in Microsoft 365 is finally there. Now, in public preview, organizations can add another phishing-resistant credential to their arsenal: **device-bound passkeys.**

_DISCLAIMER: This feature is currently in public preview. Everything you read in this blog post is subject to change and may be outdated soon. Always check the current documentation on [Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/authentication/how-to-enable-authenticator-passkey) to keep track of changes. Images in this post might be slightly different in reality._

## What is a device-bound passkey?

![](/assets/images/Reflector4_rZTD8dvS8m-506x1024.png)

First, let's have a clear understanding of the terminology. A [device-bound passkey](https://passkeys.dev/docs/reference/terms/#device-bound-passkey) is a FIDO2 Discoverable Credential bound to a single authenticator. For example, FIDO2 security keys typically hold device-bound passkeys as the credential cannot leave the device. Device-bound passkeys have been previously referred to as _single-device passkeys_.

Translating this to Microsoft 365 means you can use a device-bound passkey in the **_Microsoft Authenticator app_** on **_iOS_** and **_Android_** to perform phishing-resistant authentication towards Microsoft 365 services and Entra ID-integrated applications. This credential will be bound to the device, the mobile phone, in this case.

Why do we want passkeys? Because MFA via push, TOTP, hardware OTP, and passwordless phone sign-in can be phished and will be phished for years and years to come. We've said goodbye to passwords (hopefully). Now, it's time to take the next step.

## How to prepare for passkeys?

In a [previous](https://janbakker.tech/prepare-for-passkeys-in-entra-id/) blog post, I explained how you, as an admin, could prepare for the landing of passkeys. This is also explained in the [announcement](https://admin.microsoft.com/?ref=MessageCenter/:/messages/MC690185) in Microsoft 365 admin center >> Message center:

For your organization to opt-in to this preview, you will need to enforce key restrictions to allow specified passkey providers in your FIDO2 policy. Here are the possible configuration states for FIDO2 key restrictions during the preview:

- _No key restrictions (FIDO2 policy default)_: Tenant allows all security key models. Device-bound passkey providers on computers and mobile devices are not allowed.

- _Key restrictions set to "Allow"_: Tenant only allows the explicitly added AAGUIDs. To enable a device-bound passkey provider, add their AAGUID(s) to the key restrictions list.

- _Key restrictions set to "Block"_: Tenant blocks the explicitly added AAGUIDs and allows all other security key models. Device-bound passkey providers on computers and mobile devices are not allowed.

If you already use FIDO2 security keys, make sure you do an inventory of your current AAGUIDs so you can add them next to the device-bound passkeys. Many organizations use FIDO2 security keys for emergency break-glass admin accounts, so make sure you're not breaking that procedure. Read more: [Prepare for passkeys in Entra ID! - JanBakker.tech](https://janbakker.tech/prepare-for-passkeys-in-entra-id/)

## Prerequisites

To support passkeys in Microsoft 365, please have the following requirements in place:

- It's **_recommended_** to migrate your legacy policies for SSPR and MFA methods to the new unified policy for managing authentication methods. This will make your life easier. [Learn more](https://janbakker.tech/goodbye-legacy-sspr-and-mfa-settings-hello-authentication-methods-policies/).

- The users should have already been enrolled for multi-factor authentication, or you will need a [Temporary Access Pass](https://learn.microsoft.com/en-us/entra/identity/authentication/howto-authentication-temporary-access-pass) to enroll passkeys.

- Passkeys require **Android 14** or **iOS 17** and up.

- Microsoft Authenticator App on **Android** should be **6.2404.2444** or newer.

- Microsoft Authenticator App on **iOS** should be version **6.8.7** or newer.

- The "Usage data" (under Settings) should be enabled from the Microsoft Authenticator App.

- For Android, the Authenticator app should be enabled as an **additional provider** under Settings -> Passwords and accounts. _(user will be guided during enrollment)_

- For iOS, the Microsoft Authenticator app should be enabled for AutoFill under Settings -> Passwords -> Password options._(user will be guided during enrollment)_

- For cross-device sign-in, both the mobile phone and the device from which you sign-in should have Bluetooth enabled.

- If you already use FIDO2 security keys, make sure you do an inventory of your current AAGUIDs so you can add them next to the device-bound passkeys, as stated earlier. Don't lock yourself out.

**_One important note for Apple iPhone users_**: There is currently a limitation by Apple, so you can only use **one** password manager next to the iCloud keychain for AutoFill. Enabling the Microsoft Authenticator for AutoFill will disable any existing password manager.

![](/assets/images/10B92341-6854-4601-9669-B620320D234D-691x1024.jpeg)

Please consider reporting this to Apple by sharing feedback. [Feedback - iPhone - Apple](https://www.apple.com/feedback/iphone/)  
You can use this sample text, created by Fabian Bader:  

_Dear Tim Apple,_

_Please allow an unlimited number or at least three passkey providers in the next minor iOS release to comply with the Digital Markets Act, and to make your users happy._

_Thank you in advance_

## How to enable device-bound passkeys in Microsoft 365 / Entra ID

The easiest way to enable device-bound passkeys is by using the Entra admin center. Navigate to **Protection** -> **Authentication** methods, and select **Policies**. Find the Passkey (FIDO2) policy and enable the policy for all users or a select group of users.

![](/assets/images/image-2.png)

Next, go to the **Configure** tab.

1. Set "**_Enforce attestation_**" to "**No"**.

3. Set "**_Enforce key restrictions_**" to "**Yes"**.

5. Set "**_Restrict specific keys_**" to “**Allow**” and enter the AAGUIDs of the device-bound passkeys, and the **existing** FIDO2 security keys you support, and save the configuration.

<figure>

| iOS Microsoft Authenticator | **90a3ccdf-635c-4729-a248-9b709135078f** |
| --- | --- |
| Android Microsoft Authenticator | **de1e552d-db1d-4423-a619-566b625cdc84** |

<figcaption>

AAGUIDs for device-bound passkeys in Microsoft Authenticator App

</figcaption>

</figure>

![](/assets/images/2024-05-07-000281.png)

Some interfaces may also show a checkbox for the Microsoft Authenticator app. You can use that one to allow both iOS and Android. If you want to support either one, use the AAGUIDs.

Please find this [community-driven list of known passkey provider AAGUIDs](https://passkeydeveloper.github.io/passkey-authenticator-aaguids/explorer/). (Thanks to [Ru Campbell (@rucam365) / X (twitter.com)](https://twitter.com/rucam365)) I assume these providers will get supported in Entra over time. For now, only device-bound passkeys are supported.

You can also use Graph API to update the policies. Please make a copy of your existing configuration first because AAGUIDs may be overwritten.

Navigate to [Graph Explorer](https://aka.ms/ge) and ensure that you’ve consented to the **Policy.Read.All** and **Policy.ReadWrite.AuthenticationMethod** permissions.

Use the following URI to see the current configuration of the FIDO2 policy:

```
GET https://graph.microsoft.com/beta/authenticationMethodsPolicy/authenticationMethodConfigurations/FIDO2

```

To enable Passkeys for iOS and Android on the Authenticator App, use the following request:

```
PATCH 

https://graph.microsoft.com/beta/authenticationMethodsPolicy/authenticationMethodConfigurations/FIDO2

{
    "isAttestationEnforced": false,
    "keyRestrictions": {
        "isEnforced": true,
        "enforcementType": "allow",
        "aaGuids": [
            "90a3ccdf-635c-4729-a248-9b709135078f",
            "de1e552d-db1d-4423-a619-566b625cdc84"
        ]
    }
}

```

![](/assets/images/image-5.png)

## End-user experience - Passkey enrollment on iOS

I'm the proud owner of an iPhone so I can show you that side of the party. Go find a proud Android owner to see how that rolls. I assume it would be pretty similar.

First, sign in to [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) with some form of MFA. This can be an existing MFA method or a Temporary Acces Pass. Add a new sign-in method. Pick **_Passkey in Microsoft Authenticator (preview)._**

![](/assets/images/msedge_eRpGdgSOKL.png)

Make sure you have installed the latest version of the Microsoft Authenticator App. Then, click **Next**.

![](/assets/images/msedge_bZmJEZko4t.png)

Now, pick your mobile operation system. As said, from here, it might be slightly different, but overall, this should work the same for Android users.

![](/assets/images/image-8.png)

Make sure Autofill Passwords and Passkey is enabled from the iOS system password settings.

![](/assets/images/image-9.png)

With all the prerequisites in place, we are ready to add the passkey to the Microsoft Authenticator app.

![](/assets/images/image-10.png)

Follow the instructions, and click **I understand**.

![](/assets/images/image-11.png)

If Windows asks where to save the passkey, pick **iPhone, iPad, or Android**. This might look different, depending on the browser you are using.

![](/assets/images/image-13.png)

Use your **phone camera** (_so not the QR scan feature from the Authenticator App_) and scan the QR code.

![](/assets/images/image-36.png)

Now tick the "**Save a passkey**" banner.

![](/assets/images/image-35.png)

After the device is connected over Bluetooth, select **Authenticator** from your phone and click **Continue**.

![](/assets/images/image-16.png)

If this was successful, the passkey is now saved.

![](/assets/images/image-17.png)

  
The only thing that remains is coming up with a sensible name for your passkey.

![](/assets/images/image-18.png)

Take a moment to celebrate this memorable moment. You just added your first passkey to Microsoft 365! That's a cool story for the grandkids.

![](/assets/images/msedge_LMWTyYjClB.png)

I've created a quick video to demonstrate the steps:

## End-user experience - Direct registration of a device-bound passkey in Microsoft Authenticator – iOS + Android

It's also possible to register the passkey directly from the Authenticator app using a Temporary Access Pass.

![](/assets/images/w3FdjYyzL9.png)

![](/assets/images/hxTtpJq7hR.png)

![](/assets/images/Sx6Xi4ceYE.png)

![](/assets/images/CWkwqsxVA6.png)

![](/assets/images/Cw5yfeNnNV.png)

![](/assets/images/dJ7o9h9jKG.png)

Note: if you are also eligible for other authentication methods like Passwordless Phone Sign-in (PSI), Two-step verification with a One-time password code (TOTP), or Mult-factor authentication using push notification, these methods are also registered. For PSI, the device also needs to be registered, so additional steps are required.

If the user is only eligible for passkeys, only device-bound passkeys will be registered.

![](/assets/images/image.png)

![](/assets/images/geFUUdOjEM.png)

## End-user experience - iOS device sign-in Outlook app

1. User opens their Outlook app on their mobile device.

3. User enters their username and taps on “Next”.

_Note: If the user’s most recently used auth method is not a passkey in Authenticator app or a security key, then the user must click on “Use Face, fingerprint, PIN, or security key instead” or “Other ways to sign-in” then “Face, fingerprint, PIN, or security key” to prompt the operating system dialog in the next step._

3\. Since the user has a passkey setup in the Authenticator app, the OS prompts the user to sign-in with the passkey stored in their Authenticator app.  
4\. User goes through the bio/device PIN to sign-in with their passkey and they are then logged in.

![](/assets/images/Reflector4_meOFvYYiKv.png)

![](/assets/images/Reflector4_AHSufktcDz.png)

![](/assets/images/Reflector4_6mAjoTcTA6.png)

![](/assets/images/Reflector4_9pEQiqGzOx.png)

This experience is similar to MacOs and the Outlook app, but is only supported when the device meets the following requirements:

- Device is signed into the latest installed version of Microsoft Intune Company Portal

- Device is enrolled in mobile device management (MDM)

- Device is using the [Microsoft Enterprise SSO plug-in](https://learn.microsoft.com/en-us/entra/identity-platform/apple-sso-plugin)

## Cross-device user experience - Windows

To sign-in using a passkey on Windows, follow these steps:

![](/assets/images/msedge_yz0RBTEk3l-1-scaled.jpg)

On the sign-in screen, select Sign-in options.

![](/assets/images/msedge_RkXZRbNWg6-1024x602.jpg)

Next, pick **Face, fingerprint, PIN or security key**.

![](/assets/images/msedge_Z8KJ4gt8pb-1024x577.jpg)

Select: **Use another device**.

![](/assets/images/msedge_FyfOhvCrIX-1024x602.jpg)

In this step, select **iPhone, iPad or Android device**.

![](/assets/images/msedge_ygh6Lop9iF-1024x598.jpg)

For iOS, use the system camera to scan the QR code. Android users can also use the QR scanner from the Authenticator app. When prompted, select: **Sign in with a passkey**.

![](/assets/images/msedge_sCThmS36m3-1024x597.jpg)

Next, the device will connect over Bluetooth. After the device is connected, you will be prompted with your passkeys. Click Continue to sign in with the passkey of your choice.

## Cross-device user experience - macOS

Here's a quick overview of the experience on MacOS. To be honest, I like this better than Windows. It's straightforward, and I like the fact they combined the QR code interface to also support physical (USB) passkeys.

![](/assets/images/2024-02-13-000211.png)

![](/assets/images/2024-02-13-000212.png)

![](/assets/images/2024-02-13-000214.png)

![](/assets/images/2024-02-13-000215.png)

![](/assets/images/2024-02-13-000216.png)

## Keep track

To keep track of the registration of device-bound passkeys, you can check the [User registration details report](https://entra.microsoft.com/#view/Microsoft_AAD_IAM/AuthenticationMethodsMenuBlade/~/UserRegistrationDetails/fromNav/) in the Microsoft Entra admin center.

![](/assets/images/image-37.png)

If you are using Log Analytics or Sentinel, use this KQL query (credits to [Steven Lim](https://www.linkedin.com/posts/activity-7184406157707390977-6N3C/?utm_source=share&utm_medium=member_ios)):

```
AuditLogs
| where ActivityDisplayName == "Add Passkey (device-bound) security key"
| where Result == "success"
| extend AccountUPN = TargetResources[0].userPrincipalName
| extend AAGUID = AdditionalDetails[1].value
| extend WebAuthnInfo = AdditionalDetails[0].value
| project TimeGenerated, AccountUPN, ActivityDisplayName, AAGUID, WebAuthnInfo
```

## Wrap things up

Overall, I'm really happy that Microsoft now supports passkeys for Microsoft 365 and Entra ID-integrated apps and services, but there is still a long way to go. First of all, there is adoption. We need to educate our users on what passkeys are, why they are more secure than ever, and how to use them over various platforms. Next, vendors need to put in extra effort to optimize the user experience, especially the Windows team themselves. They need to sit down with the identity folks and think about better integration with the operation system and the current passkey features like Windows Hello. For now, it's just too many clicks and too messy. The experience on macOS is already much cleaner, so perhaps they should visit their friends at Apple sometime.

All jokes aside, phishing resistance is here to stay, and it is now available for everyone! Please share your (constructive) feedback to improve the feature over time. It might take a few months for GA, but until then, make sure to start a pilot on passkeys. You might start with your admins if they still use "legacy" MFA to protect the most desirable accounts in your tenant.

Resources:  
[How to enable Microsoft Authenticator passkey sign in for Microsoft Entra ID (preview) - Microsoft Entra ID | Microsoft Learn](https://learn.microsoft.com/en-us/entra/identity/authentication/how-to-enable-authenticator-passkey)  
[How to enable Passkeys for the Microsoft Authenticator app (ourcloudnetwork.com)](https://ourcloudnetwork.com/how-to-enable-passkeys-for-the-microsoft-authenticator-app/)  
[Passkey Public Preview for Entra ID - Cloudbrothers](https://cloudbrothers.info/passkey-public-preview-entra-id/)  
[Bluetooth Passkeys: Cross-Platform Authentication | Medium](https://medium.com/@corbado_tech/passkeys-bluetooth-cross-platform-authentication-with-qr-codes-and-bluetooth-1807265474f7)  
[Glossary - Learn About Passkeys and WebAuthn (corbado.com)](https://www.corbado.com/glossary)

Stay safe!
