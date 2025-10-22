---
title: "How to simulate risk in Microsoft Entra ID Protection"
date: 2024-03-19
categories: 
  - "entra"
  - "security"
coverImage: "image-2.png"
---

Entra ID protection is an excellent feature amongst the other services in the Entra Premium P2 license SKU. Microsoft Entra ID Protection detects identity-based risks so that admins can mitigate those risks. Users can also self-mitigate risk.

To evaluate and asses this feature, you could, of course, simulate a bunch of risky events, as described [here](https://learn.microsoft.com/en-us/entra/id-protection/howto-identity-protection-simulate-risk). Using a TOR browser and the developer tools in the browser, you can quickly bump up your sign-in risk to trigger the policies in Entra ID Protection.

To test your user risk policies, you can also use Graph API to confirm your (test)users as compromised. Sign in to the [Graph Explorer](https://aka.ms/ge) and make sure you have consented to the correct permissions. (**_IdentityRiskyUser.ReadWrite.All_**)

![](/assets/images/image-3.png)

Next, make this API call, where you replace the IDs with the ObjectIDs of your test users.

```
POST https://graph.microsoft.com/beta/riskyUsers/confirmCompromised
Content-type: application/json

{
  "userIds": [
    "29f270bb-4d23-4f68-8a57-dc73dc0d4caf",
    "20f91ec9-d140-4d90-9cd9-f618587a1471"
  ]
}
```

![](/assets/images/image-2.png)

After a few minutes, you can see that the users are actually reported, and the user risk is bumped to a high level.

![](/assets/images/image-1.png)

![](/assets/images/image.png)

This also works with PowerShell:

```
# Confirm Compromised on two users
Confirm-MgRiskyUserCompromised -UserIds "577e09c1-5f26-4870-81ab-6d18194cbb51","bf8ba085-af24-418a-b5b2-3fc71f969bf3"
```

## Dismiss risk

To programmatically dismiss the risk, you can also use Graph API:

```
POST https://graph.microsoft.com/beta/riskyUsers/dismiss
Content-Type: application/json

{
  "userIds": [
    "04487ee0-f4f6-4e7f-8999-facc5a30e232",
    "13387ee0-f4f6-4e7f-8999-facc5120e345"
  ]
}
```

Or use the Entra portal instead!

![](/assets/images/image-4.png)

## Workload Identities

If you want to simulate risk for Workload Identities, use the riskyServicePrincipal API. Using the riskyServicePrincipal API requires a Microsoft Entra Workload ID Premium license.

```
POST https://graph.microsoft.com/beta/identityProtection/riskyServicePrincipals/confirmCompromised
Content-Type: application/json

{
    "servicePrincipalIds": [
        "08396313-2477-4f6f-becb-d850b0f313a8"
    ]
}
```

Use the ObjectID of the Enterprise application (SPN) as the servicePrincipalIds

![](/assets/images/image-2.png)

Learn more:

[riskyServicePrincipal: confirmCompromised - Microsoft Graph beta | Microsoft Learn](https://learn.microsoft.com/en-us/graph/api/riskyserviceprincipal-confirmcompromised?view=graph-rest-beta&tabs=http)

The Workload Identity now shows as high risk.

![](/assets/images/image-1.png)

I hope you learned a few new tricks today! Stay safe.
