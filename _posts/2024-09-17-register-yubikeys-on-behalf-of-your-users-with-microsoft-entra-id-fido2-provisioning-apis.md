---
title: "Register Yubikeys on behalf of your users with Microsoft Entra ID FIDO2 provisioning APIs"
date: 2024-09-17
categories: 
  - "entra"
  - "security"
coverImage: "andy-kennedy-FO4CR0MnY_k-unsplash-scaled.jpg"
---

Microsoft recently announced their new FIDO2 provisioning APIs within Microsoft Entra ID. While users can register their FIDO2 keys fairly easily with a Temporary Access Pass, the new API allows admins to register keys on behalf of a user. This can be extremely handy in onboarding scenarios or in case a new key needs to be shipped to a vendor or contract worker.

The Microsoft APIs support every vendor of FIDO2 (passkeys), but Yubico has made some extra effort to provide a sample Python script that uses the Yubikey Manager under the hood. For the folks scared to touch Python (I feel ya), please go to the end of the blog post for PowerShell sample code ([creds](https://blog.icewolf.ch/archive/2024/10/25/register-FIDO2-passkey-in-entra-id-on-behalf-of-users-with-powershell/)), but don't skip the theory. It's essential to understand the process.

## How does this work?

The provisioning of security keys happens in three steps.

![](/assets/images/image-11.png)

Source: [Microsoft strengthens phishing-resistant security for Entra ID with FIDO2 provisioning APIs | Yubico](https://www.yubico.com/blog/microsoft-strengthens-phishing-resistant-security-for-entra-id-with-fido2-provisioning-apis/)

First, we ask for the [creation options](https://learn.microsoft.com/en-us/graph/api/fido2authenticationmethod-creationoptions?view=graph-rest-beta&tabs=http) from Graph API. This will give us all the required information to create the credentials on the Yubikey. The script will provide this step, but if we run it using Graph Explorer, we can get a sense of what it looks like.

![](/assets/images/image-6.png)

The next step is the 'local' part, where we need to add that credential to the Yubikey, using the information from the relying party (Microsoft). For this, we use a sample script that Yubico provided. This script will take care of the magic and talks to the Yubikey directly to create the new PIN and a new cryptographic keypair. This is done using the CTAP protocol.

![](/assets/images/image-7.png)

After this step, we can finally register the key to Entra ID, using [Graph API](https://learn.microsoft.com/en-us/graph/api/authentication-post-fido2methods?view=graph-rest-beta&tabs=http).

I expected this to be very hard to do or understand, but I got this up and running in a few minutes. Let's take a look together, shall we?

## What do we need?

- A Yubikey. For a smooth process, reset the key so it is clean.

- An app registration in Entra ID is needed to provide the correct permissions and tokens.

- An Entra ID group that holds the user(s) that you want to (bulk)provision.

- [Visual Studio Code](https://code.visualstudio.com/download)

- [Yubikey Manager](https://docs.yubico.com/software/yubikey/tools/ykman/)

- [Python](https://www.python.org/)

- [FIDO2 python module](https://github.com/Yubico/python-fido2)

- [Yubico sample scripts](https://github.com/YubicoLabs/entraId-register-passkeys-on-behalf-of-users/tree/main) (or use my [slightly tweaked version](https://github.com/BakkerJan/entraId-register-passkeys-on-behalf-of-users))

Now, I assume you can install the software requirements without guidance, but in case you need help with the Python modules, here are the commands:

```
pip install yubikey-manager
pip install fido2

```

I recommend running the script or Visual Studio terminal **as an administrator**.

## Yubico sample scripts

Download the ZIP file from GitHub and put it somewhere on your local drive. Open the folder with Visual Studio Code and open the **_configs.json_** file. This is where we must put in our credentials, the group, and other parameters. Let's take care of the credentials first.

![](/assets/images/image.png)

## Prepare Entra ID

First, let's create a new app registration in Entra ID, and add the following permissions:

**_AuthenticationMethod.ReadWrite.All_** to create (and delete) the FIDO keys.  
**_GroupMember.Read.All_** to read the provisioning group.

![](/assets/images/image-1.png)

We then create a new secret and copy the value into the configs.json file, along with the app registration's client ID and the tenant's name.

![](/assets/images/image-2.png)

The group can be a new or existing group with your test user(s).

![](/assets/images/image-4.png)

Needless to say, the users must also be enabled for passkeys in Entra ID. In this demo, it is enabled for all users. Please check your current config.

![](/assets/images/image-5.png)

I've also configured the other parameters to create a random PIN, and to delete any existing FIDO keys that exist for my user(s).

Here's what my config file looks like:

```
{
    "tenantName": "M365x341716.onmicrosoft.com",
    "client_id": "e5d678ad-36a4-428a-8f4f-c8088401ca37",
    "client_secret": "****************************************",
    "usersInScopeGroup": "FIDO Provisioning",
    "challengeTimeoutInMinutes": 60,
    "deleteExistingUserFIDOCredentials": true,        
    "useRandomPIN": true,
    "useCTAP21Features": false
}
```

## Let's go!

Cool! That was the hardest part. Now, we can see if the Python script runs. You might encounter a few missing modules, so you might want to do some trial and error.

Let's quickly explain what the script does:

**Step 1:** Start with _step1GetFIDO2Challenges.py_. This will request a token from Entra ID using the app registration and then read the group members.

It will then delete any existing keys (if you set the _deleteExistingUserFIDOCredentials_ parameter to 'true') for the user, and request the new creation options. This information will be added to the usersToRegister.csv file.

![](/assets/images/image-12.png)

**Step 2:** The next and final step is to run _step2CreateAndActivateCredential.py_. This will read the lines from the _usersToRegister.csv_ file and create a new credential for the key. This is where you input the Yubikey.

The information will then be stored in the _keysRegistered.csv_ file. If you set the _useRandomPIN_ parameter to 'true', the PIN will also be provided here.

![](/assets/images/image-13.png)

The passkey has been added to the account and is also visible in Entra ID.

![](/assets/images/image-14.png)

I've also created a quick video to explain the steps, so I hope that helps!

https://youtu.be/-WpgbX380Aw

## CTAP2.1 features

As you might have spotted, the script also provides a CTAP2.1 flag. When we set this to 'true,' we can enforce a specific PIN length and create a provisional PIN for our users.

After reading this [marvelous blog post](https://swjm.blog/forcing-pin-change-on-first-use-with-fido2-security-keys-d1d8f1a285c5), I ordered a Yubikey BIO to test the script, and it works like a charm.  

The first time the FIDO key is used with the temporary PIN, the PIN needs to be changed.

![](/assets/images/msedge_TsBS39shNe.png)

![](/assets/images/msedge_nuXTcKCHEH.png)

## PowerShell all the things

Now, as promised, I also will share a PowerShell sample script. It will do the job for one key, but it cannot handle bulk requests, provisional PIN etc. If you find a better one, please let me know so I can share it here.

```
#Install the DSInternals module
Install-Module -Name DSInternals.Passkeys

#Connect tot Microsoft Graph.
Connect-MgGraph -Scopes UserAuthenticationMethod.ReadWrite.All -TenantId <REPLACE>.onmicrosoft.com -NoWelcome

#Prep the variables 
$UPN = "<REPLACE>"
$DisplayName = "<REPLACE>"

#Get the FIDO registration options from Graph API
$RegistrationOptions = Get-PasskeyRegistrationOptions -UserId $UPN

#Create new credential on the security key (using CTAP)
$Passkey = Get-PasskeyRegistrationOptions -UserId $UPN | New-Passkey -DisplayName $DisplayName

#Prep the variables for Entra ID

$JSON = $Passkey | ConvertFrom-Json
$Attestationobject = $JSON.publicKeyCredential.response.attestationObject
$ClientDataJson = $JSON.publicKeyCredential.response.clientDataJSON
$id = $JSON.publicKeyCredential.id

#Prep the request for Graph API

$Body = @"
{
  "displayName": "$DisplayName",
  "publicKeyCredential": {
    "id": "$ID",
    "response": {
      "clientDataJSON": "$clientDataJSON",
      "attestationObject": "$Attestationobject"
    }
  }
}
"@

#Register the key in Entra ID

$URI = "https://graph.microsoft.com/beta/users/$UPN/authentication/fido2Methods"
[string]$response = Invoke-MgGraphRequest -Method "POST" -Uri $URI -OutputType "Json" -ContentType 'application/json' -Body $Body

```

## PowerShell GUI by Token2

**Update 29-11-2024**. I also wanted to share this PowerShell tool, created by Token2. [token2/fido2\_bulkenroll\_entraid: FIDO2 Key Automated Registration for Entra ID - PowerShell Solution](https://github.com/token2/fido2_bulkenroll_entraid/tree/main)

![](/assets/images/image-10.png)

## Let's wrap up

I hope this post was valuable to you.  
I'm happy to see that Yubico provided this sample code, as it will help me better understand the possibilities.  
  
With some small adjustments, these scripts can be used with any FIDO2 key. If you're a developer, I'm sure you can even build a nice front-end for this!

Here are the resources that I used:

[Microsoft strengthens phishing-resistant security for Entra ID with FIDO2 provisioning APIs | Yubico](https://www.yubico.com/blog/microsoft-strengthens-phishing-resistant-security-for-entra-id-with-fido2-provisioning-apis/)  
[Public preview: Microsoft Entra ID FIDO2 provisioning APIs - Microsoft Community Hub](https://techcommunity.microsoft.com/t5/microsoft-entra-blog/public-preview-microsoft-entra-id-fido2-provisioning-apis/ba-p/4062699)  
[YubicoLabs/entraId-register-passkeys-on-behalf-of-users: Sample code using Microsoft Graph APIs to register FIDO2 security keys for Entra ID users (github.com)](https://github.com/YubicoLabs/entraId-register-passkeys-on-behalf-of-users/tree/main)  
[Register FIDO2 Passkey in Entra ID on behalf of users with PowerShell - Icewolf Blog](https://blog.icewolf.ch/archive/2024/10/25/register-FIDO2-passkey-in-entra-id-on-behalf-of-users-with-powershell/)

Stay safe!
