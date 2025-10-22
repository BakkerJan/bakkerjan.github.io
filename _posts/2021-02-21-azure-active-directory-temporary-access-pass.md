---
title: "Azure Active Directory Temporary Access Pass"
date: 2021-02-21
categories: 
  - "entra"
  - "security"
image: "/assets/images/pexels-andrea-piacquadio-3769979.jpg"
---

This blog post is all about the new Temporary Access Pass in Azure Active Directory. At the time of writing, this feature is not officially announced, but the policy, settings, and API are now available. Time to dive in for some first experiences.

## What is a Temporary Access Pass?

As the documentation states, a Temporary Access Pass (TAP) is a time-limited passcode that serves as a strong credential and allows the onboarding of passwordless credentials. This is a big step forward, especially for companies that have to comply with the [NIST standards](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4) for onboarding and recovery.

![Identity Proofing Process](/assets/images/ProofingProcess.png)

Azure Active Directory users are now able to register their authentication methods, including passwordless authentication, without the use of an actual password. The 'password' is time-limited and administrators can make sure that it can only be used once. And the good news is: this will also work with federated domains. When a user is eligble for a TAP, the authentication will be done within Azure Active Directory. No redirection to ADFS or other federation service is needed. It can also be used for recovery purposes, when users need to re-register their FIDO2 keys or Authenticator app.

## How to enable Temporary Access Pass?

Enabling this new feature is a piece of cake. In the [Azure AD portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthenticationMethodsMenuBlade/AdminAuthMethods), go to Security -> Authentication methods, and select the Temporary Access Pass policy.

![](/assets/images/image-16.png)

Next, select the specific users, or just enable the feature for all users.

![](/assets/images/image-17.png)

![](/assets/images/image-18.png)

You can also configure additional settings, such as lifetime and length. The default lifetime is 1 hour, but you could set this value between 10 minutes and 30 days. It is also possible to require one-time use. This way, the TAP can only be used once. Keep in mind that this might cause some difficulties for your end-users. When using a one-time TAP to register a passwordless method such as FIDO2 or Phone sign-in, the user must complete the registration within 10 minutes. This limitation does not apply to a TAP that can be used more than once.

Also, make sure that you enabled the new combined registration portal for Azure MFA and Self Service Password Reset.

![](/assets/images/image-25.png)

## How to create a new TAP?

Once the policy is enabled, you are able to create your first Temporary Access Pass. The easiest way is using the Azure portal. Head over to the users' section and search for your user. Next, select the Authentication methods page, and make sure that you use the new experience.

![](/assets/images/image-19.png)

Select, **\+ Add authentication method**, and pick the Temporary Access Pass. Note that you can also add a delayed start time and that you can adjust the duration, according to the policy. If you enabled one-time use, you cannot override this setting here.

![](/assets/images/image-20.png)

When the TAP is created, you are able to copy the TAP to your clipboard. You can also see the information about the lifetime. The TAP itself will only show once, so make sure you copy it for later use. When you forgot to copy the TAP, you'll need to delete the valid TAP and create a new one. Users can only have **one** TAP.

![](/assets/images/image-21.png)

## User experience

When Grady signs-in to the security registration portal, he is not asked for this password but has to provide a Temporary Access Pass instead.

![](/assets/images/image-22.png)

When the authentication was successful, Grady can now register the preferred authentication methods. Also, he is able to delete the TAP if the registration is done.

![](/assets/images/image-24.png)

This can also be done using the Azure portal by the administrator.

![](/assets/images/image-23.png)

## Graph API

As I mentioned before, you can also make use of the Graph API. You can even use it to [enable the policy](https://docs.microsoft.com/en-us/graph/api/temporaryaccesspassauthenticationmethodconfiguration-update?view=graph-rest-beta) itself. The easiest way is to use the [Graph Explorer](https://aka.ms/ge). Here, you can add, list, and delete the TAP's for all your users.

To add a new TAP, use:

```
POST https://graph.microsoft.com/beta/users/{UPN}/authentication/temporaryAccessPassMethods
```

In the body, just put two brackets {} if you don't need any special requirements. You can also configure a delayed start and different lifetime if needed by providing extra information in the body:

```
{
  "@odata.type": "#microsoft.graph.temporaryAccessPassAuthenticationMethod",
  "startDateTime": "2021-01-26T00:00:00.000Z",
  "lifetimeInMinutes": 60,
  "isUsableOnce": false
}
```

![](/assets/images/image-26.png)

If you want to delete a TAP, you'll need the temporaryAccessPassMethods ID. You can grab this from the Azure portal, or by using this request:

```
GET https://graph.microsoft.com/beta/users/{UPN}/authentication/temporaryAccessPassMethods
```

Next, you can delete the TAP by using this API call:

```
DELETE https://graph.microsoft.com/beta/users/{UPN}/authentication/temporaryAccessPassMethods/{id}

```

## Wrap things up

Keep note that this feature is still in preview. I suggest you also check out the [official documentation](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-authentication-temporary-access-pass) to learn about the limitations. I'm very excited about this feature because it is a step closer to a full passwordless environment. This will also provide a much safer tool for onboarding and recovery use cases.

[Configure a Temporary Access Pass in Azure AD to register Passwordless authentication methods | Microsoft Docs](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-authentication-temporary-access-pass)

Stay tuned for some more goodness around this new feature!
