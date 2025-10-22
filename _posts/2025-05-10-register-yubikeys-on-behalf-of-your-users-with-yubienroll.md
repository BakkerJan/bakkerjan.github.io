---
title: "Register Yubikeys on behalf of your users with YubiEnroll"
date: 2025-05-10
categories: 
  - "entra"
  - "security"
coverImage: "andy-kennedy-FO4CR0MnY_k-unsplash-scaled.jpg"
---

In an [earlier post](https://janbakker.tech/register-yubikeys-on-behalf-of-your-users-with-microsoft-entra-id-fido2-provisioning-apis/), I showed several ways to (bulk) provision Yubikeys (or keys from other vendors) in Microsoft Entra using the provisioning APIs. In this post, we look at another gem from Yubico, [YubiEnroll](https://www.yubico.com/products/yubienroll/). This (CLI) tool is designed to delegate enrollment of Yubikeys to administrators or helpdesk staff. The good part is that it uses **delegated permissions** and respects **Admin Units**.

Although Yubico provided excellent documentation about the tool, I'd like to show you how to get this up and running.

## Entra ID App registration

First, we need to create a new app registration in Entra ID for authentication and authorization.

![](/assets/images/image-12.png)

Make sure the redirect URI is of type **Public client/native (mobile & desktop)** and has **http://localhost/yubienroll-redirect** as the value.

Next, assign **delegated** permissions to Graph API and grant admin consent for your tenant.

_User.ReadBasic.All  
AuthenticationMethod.ReadWrite.All_

![](/assets/images/image-14.png)

## Download YubiEnroll

Next, download the [YubiEnroll MSI](https://downloads.yubico.com/support/yubienroll-1.1.0-win64.msi) and install it on Windows. After installation, the app can be found in C:\\Program Files\\Yubico\\YubiEnroll.

## Configure the provider

First, we need to configure the tool to talk to Entra ID.

Open the command promt and browse to C:\\Program Files\\Yubico\\YubiEnroll and type:

```
yubienroll providers add entra
```

![](/assets/images/image-15.png)

Follow the instructions and provide the information about the app registration and endpoints. Entra ID and Graph API URL's can be set to default, unless you use national cloud deployments. These endpoints can be found [here](https://learn.microsoft.com/en-us/graph/deployments#app-registration-and-token-service-root-endpoints).

```
provider = "ENTRA"
client_id = "<YOUR CLIENT ID HERE>"
redirect_uri = "http://localhost/yubienroll-redirect"
tenant_id = "<YOUR TENANT ID HERE>"
entra_base_url = "https://login.microsoftonline.com"
graph_base_url = "https://graph.microsoft.com"
```

All provider configurations used by YubiEnroll are stored in a single _yubienroll.toml_ file that can be manually edited. The file is located at %APPDATA%\\yubico\\yubienroll\\yubienroll.toml

Using a tool like Intune, you can distribute the application and put the config file in place, so the tool is ready to use for your admins.

## Create Enrollment Profile

Once you have the provider in place, we can create your first enrollment profile for Yubikeys. These parameters will tell how your keys are provisioned. Not all features might be supported, as this depends on the type and firmware of your key. In my tests, I used an old(er) key (did not want to reset my Yubikey BIO), so it does not support provisional PIN and such.

```
yubienroll profiles add Profile1
```

![](/assets/images/image-19.png)

This profile will reset my Yubikey and set a random 6-digit PIN. The configuration options “Min PIN length,” “Require always UV,” and “Force PIN change before use” are only supported for YubiKeys with firmware version 5.5 and higher.

Your local _toml-file_ will look (something) like this, and you are ready to enroll your first Yubikey!

![](/assets/images/image-20.png)

## Authenticate

Now we need to sign in to Entra ID to get a token. The following cmdlet can do this:

```
yubienroll login --no-launch-browser
```

Copy the string to your browser, and authenticate accordingly.

![](/assets/images/image-22.png)

You can leave the _\--no-launch-browser_ parameter, but then it will launch in your last-used browser automatically.

## Enroll the key

Now we have everything in place to enroll a Yubikey. Let's register a new Yubikey for Emma Brooks. All we need is the UserPrincipalName, _Emma.Brooks@entrasec.onmicrosoft.com_, in this case. Now run:

```
yubienroll credentials add Emma.Brooks@entrasec.onmicrosoft.com
```

![](/assets/images/image-23.png)

If you have multiple profiles, you can specify the profile by using this cmdlet:

```
yubienroll credentials add Emma.Brooks@entrasec.onmicrosoft.com --profile another-profile

```

After confirmation, the key is reset, and the credential is written to the key and Entra ID.

![](/assets/images/image-24.png)

We can see the Yubikey is added to Emma's account.

![](/assets/images/image-25.png)

The tool is also capable of removing keys if needed.

```
yubienroll credentials delete Emma.Brooks@entrasec.onmicrosoft.com
```

To list the current keys:

```
yubienroll credentials list Emma.Brooks@entrasec.onmicrosoft.com
```

![](/assets/images/image-26.png)

## Admin Units

Since delegated permissions are used, Admin Units will be respected.

![](/assets/images/image-29.png)

Alex Stone, part of the local IT helpdesk, can create Yubikeys for Daniel and Emma, but is blocked if he tries to make one for Michael.

![](/assets/images/image-30.png)

## Let's wrap up

Thanks to [Danny](https://www.linkedin.com/in/danny-van-zon-59580b1a4/) for pointing me to this tool. I really like it for several reasons:

- MSI for easy installation.

- Ability to stage the config file with the tool, so admins do not have to set it up.

- Using delegated permissions, with respect to Admin Units.

- Perfect for operational support, such as onboarding and recovery.

- Multiple profiles can be used.

- Supports advanced FIDO features like provisional PIN and PIN complexity.

To name a few limitations:

- It can only be used with Yubikeys.

- No bulk enrollment ([Yubico has other tools for that](https://janbakker.tech/register-yubikeys-on-behalf-of-your-users-with-microsoft-entra-id-fido2-provisioning-apis/))

- Logout for Entra does not work.

- No GUI at the moment. Admins need some guidance on the CLI prompts.

## Resources

As stated before, the docs on this tool are really good, so always check the latest here:

[Introduction — YubiEnroll User Guide documentation](https://docs.yubico.com/software/yubikey/tools/yubienroll/intro.html)

## Learn by watching

I also created a short video for folks who learn better by watching content.

https://youtu.be/VBhmpkGaKdo

Stay safe!
