---
title: "Duplicate Azure Active Directory Conditional Access policies"
date: 2023-02-20
categories: 
  - "entra"
  - "security"
image: "/assests/images/pexels-dids-5782237-scaled.jpg"
---

In this post, we look at managing Conditional Access policies and, in particular, duplicating existing policies. This can be super handy when you:

- Want to troubleshoot a specific policy and be able to tweak the non-production version.

- Are using a ring-based approach and need to duplicate a set of policies quickly.

- Want to create new policies based on JSON templates.

This can be done in various ways, but today we use two different methods:

- Using the new Conditional Access controls

- Using Graph API
    

## Duplicate a policy using the Conditional Access UI

Now in public preview, Microsoft refreshed the interface and enhanced the user experience with an updated design and a few new improvements. One of them is the ability to duplicate existing policies.

Find your "source" policy, and click the three dots at the end of the policy. Then select **Duplicate**.

![](/assets/images/image.png)

Next, increase the version number, or add COPY at the end of the policy name. Also decide how the new policy is created by selecting the Report-only, On, or Off button. Click **Create** to duplicate the policy.

![](/assets/images/image-1.png)

You can now see the duplicated policy. Easy right?

![](/assets/images/image-2.png)

## Duplicate a policy using Graph Explorer and Graph API

A more advanced and flexible way to duplicate policies can be done by using the Graph API and Graph Explorer. First, we need to capture the ID of the "source" policy. This can be easily found using the Azure portal.

![](/assets/images/image-3.png)

Next, head over to the [Graph Explorer](https://aka.ms/ge), and make sure you are signed in with the right permissions. If you are getting an error, check the permissions tab, and give consent to the Policy.Read.All permission.

![](/assets/images/image-4.png)

Next, request the existing policy payload by adding this query: (replace the ID with the ID that you captured in the previous step)

```
GET https://graph.microsoft.com/beta/identity/conditionalAccess/policies/{id}
```

You should see a status **_200_** response and the JSON payload of the source policy.

![](/assets/images/image-5.png)

Now, for our next step, we need to copy the response of the previous step and paste it into the request body:

![](/assets/images/image-6.png)

Now, we need to make a few modifications to the payload before we can use it as a new policy:

1. Change the operator from **GET** to **POST**

3. Delete the policy ID from the query

5. Set the status property to '**_disabled_**'

![](/assets/images/image-7.png)

Next, delete the following entries from the request body:

- @odata.context

- id

- createdDateTime

- modifiedDateTime

The payload will look like this:

```
{
    "displayName": "MFA for Alex Wilber - V1.1",
    "state": "disabled",
    "sessionControls": null,
    "conditions": {
        "userRiskLevels": [],
        "signInRiskLevels": [],
        "clientAppTypes": [
            "all"
        ],
        "servicePrincipalRiskLevels": [],
        "platforms": null,
        "locations": null,
        "times": null,
        "deviceStates": null,
        "devices": null,
        "clientApplications": null,
        "applications": {
            "includeApplications": [
                "None"
            ],
            "excludeApplications": [],
            "includeUserActions": [],
            "includeAuthenticationContextClassReferences": []
        },
        "users": {
            "includeUsers": [
                "1dec885a-b241-46b8-b382-c83dcb1f22cc"
            ],
            "excludeUsers": [],
            "includeGroups": [],
            "excludeGroups": [],
            "includeRoles": [],
            "excludeRoles": [],
            "includeGuestsOrExternalUsers": null,
            "excludeGuestsOrExternalUsers": null
        }
    },
    "grantControls": {
        "operator": "OR",
        "builtInControls": [
            "mfa"
        ],
        "customAuthenticationFactors": [],
        "termsOfUse": [],
        "authenticationStrength@odata.context": "https://graph.microsoft.com/beta/$metadata#identity/conditionalAccess/policies('afd00c63-5329-4475-9489-36d9d1558e12')/grantControls/authenticationStrength/$entity",
        "authenticationStrength": null
    }
}
```

![](/assets/images/image-8.png)

Now, run the query and check if the payload is accepted and the policy created.

![](/assets/images/image-9.png)

The new policy should be available in the Azure portal as well.

![](/assets/images/image-10.png)

## Wrap things up

See how easy that was? Before this new feature, I would find myself and other admins creating new policies manually, but those days are now gone. It also gives a really nice step up if you consider deploying and managing your policies as code. If you are not sure where to start, please check out the new templates feature, where you can grab the JSON payload as well.

![](/assets/images/image-11.png)

  
Learn more:  
[Conditional Access templates - Azure Active Directory - Microsoft Entra | Microsoft Learn](https://learn.microsoft.com/en-us/azure/active-directory/conditional-access/concept-conditional-access-policy-common)  
  
Stay safe!
