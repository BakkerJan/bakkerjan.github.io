---
title: "Customize the MFA registration policy in Azure AD Identity Protection"
date: 2021-10-06
categories: 
  - "entra"
  - "security"
image: "/assests/images/pexels-tim-samuel-5835356.jpg"
---

```
Disclaimer: this is a proof of concept, not something that is supported or recommended by me or Microsoft. Needless to say, don't do this in your, or your customers' (production) environment. This article points to internal API's, and those are most likely be changed over time. With that out of the way: on with the show! 
```

## What are we looking at?

As [discussed before](https://janbakker.tech/getting-everyone-enrolled-for-azure-mfa-and-sspr-how-hard-can-it-be/), there are a lot (and counting) ways to enroll for multi-factor authentication in Azure AD. Two of those methods have the possibility to postpone the registration for 14 days:

- Security Defaults
- Azure AD Identity Registration policy (MFA registration policy)

Now both settings come with this **14** days grace period where the user can skip the registration. I often got the question if it's possible to change that value, and until now that answer was rather disappointing. Unfortunately, there is no way to configure this in the GUI, and there is no documentation on how to do that through PowerShell or Graph API either. So, then there is one other way to figure this out: reverse engineering. 😎

<figure>

- <figure>
    
    ![](/assets/images/image-7.png)
    
    <figcaption>
    
    Default behavior Identity Protection
    
    </figcaption>
    
    </figure>
    
- <figure>
    
    ![](/assets/images/1633455721.png)
    
    <figcaption>
    
    Default behavior Security Defaults
    
    </figcaption>
    
    </figure>
    

</figure>

## Some context first

The good news is, that Security Defaults and Identity Protection are somehow intertwined. Azure AD Identity Protection is a premium feature (P2), but if you enable Security Defaults (free) you'll get a part of that premium feature as a gift from Microsoft. It uses the registration policy functionality and the risk-based MFA approach. This means that users only get prompted for MFA if there is an unusual activity like a new device or location.

In this post, we focus mainly on Azure AD Identity Protection. I had a look at Security Defaults, but because this feature is "just a toggle", there is [not that much](https://docs.microsoft.com/en-us/graph/api/identitysecuritydefaultsenforcementpolicy-update?view=graph-rest-1.0&tabs=http) that you can (why would you?) change on that.

In order to use Azure AD Identity Protection, you will need a premium Azure AD (2) license. If you don't have that license, you can simply start a 30 days trial. After a few minutes, your tenant will be enriched with the P2 features.

![](/assets/images/image-11.png)

## Get the right tools

Many browsers these days are equipped with a nifty tool that makes life way much easier: Developer tool. Now, don't let that name scare you, we are all developers somehow, so this tool is for everybody! With this feature, you are able to see what's happening under the hood of your web pages and applications. It can be used for troubleshooting, learning, capturing media sources, and reverse engineering like we do today. In this demo, I use Microsoft Edge, but this might also work in other browsers as well.

Let's head over to our Azure portal, and go to [Identity Protection -> MFA registration policy.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/IdentityProtectionMenuBlade/MfaPolicy) From there, hit the menu, look for **More tools -> Developer tools**.

![](/assets/images/image-8.png)

To capture the API that we are looking for, select the **network** tab. If you never used that tab before, you can select that with the **+** (plus) button.

Make sure that the session is recording, and enable (or disable) the MFA registration policy. The events are now captured. If you like, stop the recording, so you keep a cleaner overview.

![](/assets/images/image-9.png)

Now, we select the event that holds the API request for the change that we just made. Select the headers tab to have a better view, and look for the API request that looks like this:

```
https://graph.windows.net/39a5568b-aafd-430a-a6a5-715bd29ce240/policies/43f2e7b2-7cfe-4aa3-8e1b-207f71cc12ba?api-version=1.6-internal
```

![](/assets/images/image-12.png)

If you found the right request, right-click on that request and choose **_Copy -> Copy as PowerShell_**

![](/assets/images/image-13.png)

Now fire up your favorite PowerShell editor, and paste the payload.

1. This is the API that is being called. The first part holds the tenant ID, and after that the policy ID.
2. When you hit that save button, the API call is using PATCH as the method.
3. This is the Bearer token for authentication and authorization.
4. The body parameter hold the content of the policy.

![](/assets/images/image-14.png)

Let's focus on the value in the body parameter (4). Because this is a large string, I copied it to an editor to have a better look. As you can see, you can find all the settings of the policy, the included and excluded groups, and lucky for us also the value that holds the grace period of 14 days.

![](/assets/images/image-15.png)

Back in our PowerShell editor, look for the _RegistrationWindowInDays_ value and change to any value you like, and hit the _Run script_ button to replay the request.

![](/assets/images/image-16.png)

If the request is successful, you will see **204** as the StatusCode.

![](/assets/images/image-18.png)

To quickly see if the value is updated:

1. Put the request into a variable by adding _$current=_ to the Invoke-Request
2. Change the method from PATCH to GET
3. Comment the payload out of the request by adding # in front of the lines. These lines will be ignored.
4. Add your variable to the script by adding _$current.RawContent_ to show the current settings.

![](/assets/images/image-19.png)

Now keep in mind that your token might be expired. The easiest way to grab a new token is to get it from the Azure portal. Refresh the browser, look for another API request and copy the Bearer value from under the Request headers section. Replace that value with the Bearer token in the PowerShell request.

![](/assets/images/image-17-1024x537.png)

## Did it work?

After you changed the value, let's see if it actually works. Sign in with a user that has not registered for MFA yet to see the outcome.

- ![](/assets/images/image-20.png)
    
- ![](/assets/images/1633500354.png)
    

There we go! As you can see the value does now show up, and as you can see, you can go nuts with the numbers here.

## So what about Security Defaults?

When you enable Security Defaults, this setting gets overruled. Well, at least most of the time. You could somehow enable both Security Defaults and the MFA registration policy together, but the outcome is really inconsistent.

![](/assets/images/image-21.png)

This behavior is reproducible by also changing the _IsEnabled_ value to **true** in the Security Defaults section.

![](/assets/images/image-22.png)

This action will enable **both** MFA registration policy and Security Defaults, something that's not possible through the Azure portal anyway. As I said, these policies will keep fighting, and the user is prompted with either one or sometimes both.

![](/assets/images/image-24.png)

## Let's wrap up

Anyway, this was a fun experience, and although it is not a good idea to implement these types of workarounds in production, I hope you learned a thing or two out of this. The Developer tool in Edge is a tool that deserves more attention in the first place. And I didn't know that you could copy the payload to PowerShell or other formats either. This can be really helpful to set up your automation scripts for example.

I hope you had fun, stay safe!
