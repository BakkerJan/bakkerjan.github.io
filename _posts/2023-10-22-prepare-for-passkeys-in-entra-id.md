---
title: "Prepare for passkeys in Entra ID!"
date: 2023-10-22
categories: 
  - "entra"
  - "security"
image: "/assests/images/5caeb71d-a756-4e5c-8b5c-924f653d7bda.jpeg"
---

[Only a few months](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/whats-new#public-preview---device-bound-passkeys-as-an-authentication-method) until Microsoft Entra ID will support device-bound passkeys stored on computers and mobile devices as an authentication method in preview, in addition to the existing support for FIDO2 security keys. This enables your users to perform phishing-resistant authentication using the devices that they already have.  

## What is a device-bound passkey?

First, let's zoom in a little on [device-bound passkeys](https://passkeys.dev/docs/reference/terms/#device-bound-passkey). This is a FIDO2 Discoverable Credential that is bound to a single authenticator. For example, FIDO2 security keys typically hold device-bound passkeys as the credential cannot leave the device. Device-bound passkeys have been previously referred to as single-device passkeys.

For this reason, the current FIDO2 security methods policy in Entra ID will be expanded to support the use of passkeys. So, if your organization already uses FIDO2 security keys for phishing-resistant authentication (good job!), it's time for action!

![](/assets/images/2023-10-22-000193.png)

If your organization requires or prefers FIDO2 authentication using physical security keys only, then please enforce key restrictions to only allow security key models that you accept in your FIDO2 policy. Otherwise, the new preview capabilities enable your users to register for device-bound passkeys stored on Windows, macOS, iOS, and Android.

## Key Restriction Policy

You can use the FIDO2 Key Restriction Policy to specifically block or allow types of FIDO2 keys. This is done based on the _AAGUID_ of the key. Most of the FIDO2 security keys have a unique AAGUID that can be used to either block or allow the key. In this example, I've blocked one specific key in Entra ID.

![](/assets/images/2023-10-22-000194.png)

## Where can I find these AAGUIDs?

Most of these AAGUIDs can be found on the website of the vendor. For example:  
  
[YubiKey Hardware FIDO2 AAGUIDs – Yubico](https://support.yubico.com/hc/en-us/articles/360016648959-YubiKey-Hardware-FIDO2-AAGUIDs)  
[Products – FIDO Security Keys (ftsafe.com)](https://fido.ftsafe.com/products/)  

The AADGUID can also be found in the Microsoft Entra admin center under the authentication methods blade of the user.

![](/assets/images/2023-10-22-000195.png)

Graph API can also show the AAGUID when running:

```
GET https://graph.microsoft.com/beta/users/userID/authentication/methods
```

![](/assets/images/2023-10-22-000196.png)

Here's a script by [Fabian Bader](https://twitter.com/fabian_bader) to do an inventory on existing FIDO AAUGUIDs: [List all AAGUIDs in an Entra ID / Azure AD tenant (github.com)](https://gist.github.com/f-bader/63a1a09a0b8b04b05b08e876ef3a7a19)

Or check this method, created by [Nathan McNulty](https://twitter.com/NathanMcNulty):

```
Install-Module Microsoft.Graph

Connect-MgGraph -Scope AuditLog.Read.All,UserAuthenticationMethod.Read.All

((Get-MgReportAuthenticationMethodUserRegistrationDetail -Filter "methodsRegistered/any(i:i eq 'passKeyDeviceBound')" -All).Id | ForEach-Object { Get-MgUserAuthenticationFido2Method -UserId $_ -All }).AaGuid | Select-Object -Unique
```

## Time for action

Microsoft will use the FIDO2 authentication method UX to integrate the new device-based passkeys into Entra ID. So, when you're currently using FIDO2 keys, but don't use the Key Restriction Policy, device-based passkeys will be enabled by default. If you want to control the use of passkeys, make sure you only allow the FIDO keys you currently support/allow in your tenant. This will automatically block the device-bound passkeys when it arrives.

Be aware that key restrictions set the usability of specific FIDO2 methods for both registration and authentication. If you change key restrictions and remove an AAGUID you previously allowed, users who previously registered an allowed method can no longer use it for sign-in.

Learn more:

[Passwordless security key sign-in - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/authentication/howto-authentication-passwordless-security-key)  
[What's new? Release notes - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/whats-new#public-preview---device-bound-passkeys-as-an-authentication-method)
