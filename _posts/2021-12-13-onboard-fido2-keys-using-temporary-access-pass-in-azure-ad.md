---
title: "Onboard FIDO2 keys using Temporary Access Pass in Azure AD"
date: 2021-12-13
categories: 
  - "entra"
  - "security"
image: "/assets/images/IMG_3928-scaled-e1639428106559.jpg"
---

One of the requirements to use FIDO2 security keys with your Microsoft 365 or Azure Active Directory account is multi-factor authentication. So if the user has not added an authentication method, they need to do that first, in order to add the FIDO2 security key to the account. That is sort of a chicken and egg situation.

To work around that, we can use Azure Active Directory's Temporary Access Pass (TAP) to onboard the user. Using this method, TAP will statisfy the MFA requirement. Users can use TAP to bootstrap passwordless methods such as Windows Hello, FIDO2 keys, and Microsoft Authenticator App. In this blogpost, we take a look at how to set that up in your environment.

## First things first

To support FIDO2 keys as authentication method, we need three things in place:

- Combined registration portal for MFA and SSPR enrollement
- Authentication policy for FIDO2
- Authentication policy for Temporary Access Pass

The new combined registration experience is enabled by default on newer tenants, but if you have an older tenant, go to **Azure Active Directory-> User Settings -> Manage user feature settings**, and make sure that users can use the combined security information registration experience.

- ![](/assets/images/image.png)
    
- ![](/assets/images/1639409058.png)
    

Next, go to **Azure Active Directory -> Security -> Authentication methods**, and make sure that both FIDO2 Security Key and Temporary Access Pass is enabled for all, or a selected group of users.

![](/assets/images/image-4.png)

You can configure additional settings to restrict specific keys for example. You can also change the default settings for the Temporary Access Pass. For now, we leave this to default.

- ![](/assets/images/image-2.png)
    
- ![](/assets/images/1639409772.png)
    

## End-user experience

Now, if a user has not setup any authentication method before, they are prompted with the following error when they try to register a new key at [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).

_"To set up a security key, you need to sign in with two-factor authentication."_

![](/assets/images/image-3.png)

This is where the Temporary Access Pass comes to play. Let's create one for our test user.

For the specific user, go to the **Authentication methods** blade, and add a new Temporary Access Pass.

![](/assets/images/image-5.png)

For this usecase, we set the **_One-time use_** switch to **Yes.** This way, the TAP will expire after its been used. After the TAP is created, the required information is shown in the portal:

![](/assets/images/image-6.png)

Now, as the user, go to [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Make sure it's a fresh session, meaning that the user is not signed in. Ideally, use a private browser session. Enter the username, and click **_Next_**.

![](/assets/images/image-7.png)

Now, instead of the password, the user is asked to enter the Temporary Access Pass.

![](/assets/images/image-8.png)

After you've signed-in, click **_\+ Add method_**, and select Security key from the dropdown menu.

![](/assets/images/image-9.png)

Depending on the type of key you are using, select USB or NFC device. In my case, I use the Authentrend ATKey.Pro, so I select USB. I have already enrolled the key with my fingerprint, using the [standalone enrollment](https://janbakker.tech/this-might-be-the-fido2-key-for-you-authentrend-atkey-pro/) (with powerbank).

![](/assets/images/image-10.png)

The next steps are pretty straight forward, but I'll show all the screens to get an idea of the entire process.

![](/assets/images/image-11.png)

![](/assets/images/image-12.png)

<figure>

![](/assets/images/image-13.png)

<figcaption>

Click OK to give access to the device information

</figcaption>

</figure>

<figure>

![](/assets/images/image-14.png)

<figcaption>

If not already inserted, insert the security key into your device.

</figcaption>

</figure>

![](/assets/images/71KRmnoW65S._AC_SL1500_.jpg)

_If this is a new key, the user is prompted to set up a new PIN for this device. The ATKey.Pro has a built-in fingerprint reader, that can be used as well. If you enrolled your fingerprint earlier, a PIN is not required._

![](/assets/images/image-18-1024x652.png)

<figure>

![](/assets/images/image-17.png)

<figcaption>

After setting the PIN, user must touch the key to prove physical presence

</figcaption>

</figure>

<figure>

![](/assets/images/image-15.png)

<figcaption>

Give the key a proper name. If you have more than 1 key, make sure you can distinguish them from each other

</figcaption>

</figure>

![](/assets/images/image-16.png)

And that's it! The user can now sign in using the FIDO2 security key, and does not have to provide a password anymore. As you can see, the Temporary Access Pass is expired after one time use. Note that the regular password can still be used.

![](/assets/images/image-19.png)

## Wrap things up

Temporary Access Pass can be used to enroll your users into passwordless methods like we have seen in this blog post. If you have an existing process or application for user onboarding in place, you can make use of [the Graph API to create](https://docs.microsoft.com/en-us/graph/api/temporaryaccesspassauthenticationmethod-post?view=graph-rest-beta&tabs=http) TAPs for your users. Or you can build [a PowerApp](https://janbakker.tech/how-to-build-a-powerapp-temporary-access-pass-manager-part-1/) for your helpdesk staff like this example:

![](/assets/images/image-72.png)

Learn more about Temporary Access Pass:  
[Azure Active Directory Temporary Access Pass - JanBakker.tech](https://janbakker.tech/azure-active-directory-temporary-access-pass/)  
[Temporary Access Pass is now in public preview - Microsoft Tech Community](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/temporary-access-pass-is-now-in-public-preview/ba-p/1994702)

Stay safe!
