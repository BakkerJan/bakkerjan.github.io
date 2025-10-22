---
title: "Going passwordless with the FEITIAN Fingerprint card"
date: 2020-05-17
categories: 
  - "entra"
  - "security"
  - "windows-10"
tags: 
  - "azuread"
  - "biometrics"
  - "feitian"
  - "fido2"
  - "passwordless"
  - "pin"
coverImage: "image-47.png"
---

```
A quick word upfront. I'm not a salesperson. I'm interested in FIDO2 because it delivers passwordless and strong authentication. That means that you should be free using any FIDO2 security key or card you want. Whether it's Yubico, FEITIAN, Solo, or any other brand. USB-A, USB-C, NFC, Bluetooth, Lightning, with or without biometrics. This blog is not what to buy and where to buy. This blog is about security.  That being said: on with the show!  
```

* * *

I've tested a bunch of FIDO2 security keys so far, and one thing I've learned: you better have two (or more). I used the FEITIAN K33 for a while and I kept it in the small pocket in my jeans. One time, I forgot to take it out and it ended up in the washing machine. So my conclusion was:

1. Great key
2. Not water-resistant.

- ![](/assets/images/201907041918078979.png)
    
- ![](/assets/images/190719115245-jeans-detail-full-169.jpg)
    

Today, we take a look at a card that more likely will survive a ride in the washing machine (not tested yet ;) ) It's the new FEITIAN fingerprint card. This one has the size of a credit card and will fit in your wallet or cardholder. So less chance it will end up someplace wet. The card is equipped with FIDO2. And that's where things get's interesting.

#### FIDO? Sounds like a dog's name

FIDO stands for Fast IDentity Online, and comes from the Latin word fido, which means “to trust”. And it was the name of Abraham Lincoln's dog, so you are right about that part. FIDO2 gives both passwordless and multi-factor authentication with either biometrics of a PIN.

![](/assets/images/image-72.png)

#### Setup

In this setup, I'll use the FEITIAN Fingerprint Card. But the steps are (almost) the same for any FIDO2 security key. So when I say card, you might as well read 'key'. Just make sure that you choose the right type of key/card when you register it (NFC or USB)

<figure>

![](/assets/images/image-47.png)

<figcaption>

The card itself has no battery!

</figcaption>

</figure>

To use the card, the device you use must have a card reader. In this setup, I use the FEITIAN [R502CL Contactless USB-C Reader](https://www.ftsafe.com/Store/Product?id=13)

![](/assets/images/201908292206185d67dbda157ca.png)

Next, I have Windows 10 device, version 2004. The machine is a member of an Azure AD domain. Windows Hello for Business is enabled. I have at least 1 MFA method registered in Azure AD. This is required before you can add any FIDO2 security key or card to Azure AD.

#### Register your card to use with Azure AD / Office 365

To use your card (or key) with Aure AD or Office 365 applications, you'll need to register it first. This can only be done using the new [combined registration portal](https://janbakker.tech/2020/05/02/what-admins-should-know-about-the-combined-registration-portal-for-azure-mfa-and-self-service-password-reset/) for MFA and SSPR, and you should be allowed by your administrator to register FIDO2 Security keys.

![](/assets/images/image-48.png)

###### Make sure the card reader is connected to your device, and the correct driver is installed before you begin the registration process!

1. Log in to [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo). Make sure you have registered **_at least one_** method for MFA.
2. Click "add method" and choose for Security key

![](/assets/images/image-51.png)

3\. Select NFC device  
4\. Have your key ready, and click Next.

![](/assets/images/image-52.png)

5\. Follow the instructions on the screen.

- ![](/assets/images/image-53.png)
    
- ![](/assets/images/image-54.png)
    
- ![](/assets/images/image-55.png)
    

6\. If this is the first time that you use the card, you'll have to pick a PIN. Enter the PIN twice.

![](/assets/images/image-57.png)

7\. After you entered the PIN, pick a name for the card.

![](/assets/images/image-58.png)

Done! Your card is now registered and ready to use!

#### End-user experience (browser)

You can now use your card/key on all websites that support Web Authentication (WebAuthn). WebAuthn is currently supported in Google Chrome, Mozilla Firefox, Microsoft Edge and Apple Safari (preview) web browsers, as well as Windows 10, and Android platforms.

Head over to [https://portal.office.com](https://portal.office.com). As you can see, you can enter your name and password to log in, but choose Sign-in options instead and pick "Sign in with a security key". Just tap your card on the reader and type your PIN to log in.

- ![](/assets/images/image-60.png)
    
- ![](/assets/images/image-61.png)
    
- ![](/assets/images/image-62.png)
    

#### Adding the fingerprint (using Windows 10)

The card we're using has a fingerprint sensor. We can use our fingerprint instead of the PIN. Therefore, we need to register the fingerprint first.  
The card can store up to 8 different fingerprints.

Since Microsoft has already integrated FIDO2 protocol to its latest Windows OS 1903 or above, the process of fingerprint enrollment can be done through Windows settings page: **Settings -> Accounts -> Sign-in options -> Security Key.**

![](/assets/images/image-65.png)

Click **Manage** and choose to set up your fingerprint.

![](/assets/images/explorer_iW8ycbZf80.png)

Follow the steps to add one or more fingers.

- ![](/assets/images/explorer_g7l0anBrQn.png)
    
- ![](/assets/images/explorer_qwcJKfjrRo.png)
    
- ![](/assets/images/explorer_9tfIhZQ1Zq.png)
    
- ![](/assets/images/explorer_ORxWSbfcdU.png)
    
- ![](/assets/images/explorer_JX8Ip9viJZ.png)
    

Now our fingerprint is added to the card, we can log-in using our fingerprint. I created this short video to show you how this works. Note that you don't have to provide your username, password, or PIN.

#### Additional way to enroll a fingerprint

You can add fingerprints to the card using the [Android](https://play.google.com/store/apps/details?id=com.ftsafe.FingerPrint&rdid=com.ftsafe.FingerPrint&pli=1) or [iPhone app](https://apps.apple.com/cn/app/fingerprint-card-manager/id1447629537?l=en). You'll need an additional holder to power the BLE Bluetooth chip. This is only needed to enroll the fingerprints.

![](/assets/images/image-70.png)

![](/assets/images/image-71.png)

#### Windows 10 - Hello for Business

Azure AD domain joined or hybrid devices can use security keys (or security cards in this case) to experience the passwordless sign-in. Windows Hello for Business already gives that features by replacing passwords with biometrics of PIN. Starting from Windows 1809 (1903 for the best experience), you can enable security keys for Windows 10 users. This can be multiple ways, depending on your scenario:

- [Enable with Intune](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#enable-with-intune)
- [Targeted Intune deployment](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#targeted-intune-deployment)
- [Enable with a provisioning package](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#enable-with-a-provisioning-package)
- [Enable with Group Policy (Hybrid Azure AD joined devices only)](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#enable-with-group-policy)

I suggest taking a look at this page: [https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#enable-security-keys-for-windows-sign-in](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#enable-security-keys-for-windows-sign-in)

The user experience is almost the same as shown in the video. You just select the security key method at the login screen, place your card in the reader and put your finger on the fingerprint reader on the card.

![](/assets/images/image-69.png)

#### LED indicators

Sometimes the interface from the device or browser you are using can be a bit unclear about the log-in process. I've noticed that, when you use a PIN, the interface will ask you to provide the PIN, but when you use the fingerprint, the interface will not ask for that. The green LED on the card just rapidly blinks. Good to know what each LED on the card means.

| Green LED ON | Fingerprint verification success |
| --- | --- |
| Red LED ON | Fingerprint verification fails |
| Green LED blinks slowly | Power up |
| Green LED blinks rapidly | Need to verify fingerprint/Need to sample fingerprint |
| Blue LED blinks | Bluetooth is ready to connect |
| Blue LED ON | Bluetooth is connected |

![](/assets/images/Passwordless-1.jpg)

#### Let's wrap up

I'm honest. Going [passwordless](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2KEup) is not that easy. It's a journey. Take it step by step. Take a look at the different ways to provide passwordless authentication to your users. Whether its Windows Hello for Business (platform), Microsoft Authenticator app (software) or FIDO2 security  
devices (hardware). Pick the right solution for the right scenario.

![](/assets/images/image-73.png)

Stay safe!
