---
title: "Use Requestor information in Entra ID Access Packages as input for Custom Extensions"
date: 2024-12-06
categories: 
  - "entra"
image: "/assests/images/explorer_Zi6xxgjNFL.png"
---

In a [previous](https://janbakker.tech/request-temporary-access-pass-on-behalf-of-others-via-entra-id-governance-access-package/) blog post, I explained a proof of concept in which we use Entra ID Governance Access Packages to request a Temporary Access Pass on behalf of other users.

![](/assets/images/Presentation2.png)

While this is already a good starting point, I'd like to take it to the next level. In the previous example, we had some static values like the duration of the Temporary Access Pass and the phone number to which it should be sent. This was hard-baked into the Logic App, but in this post, we will make some adjustments to make them dynamic so that the requester can customize the request.

My goal is to leverage the Request info feature to gather some extra information from the requester before creating the Temporary Access Pass. Now, this is just an example of what's possible, not a best practice for delivering Temporary Access Passes.

I want the requestor to be able to customize the following values:

- **Temporary Access Pass type**. Can it be used more than once?

- **Temporary Access Pass lifetime.** How long should the TAP be valid?

- The **mobile phone number** to which the TAP should be sent.

As a bonus, the friendly name of the TAP lifetime should be added to the SMS. This value is also dynamic.

## Prepare the questions

First, we must edit our access package policy to include our questions. This can be done from the Requester information blade.

![](/assets/images/image-12.png)

**Question 1**: Can the Temporary Access Pass be used more than one time?  
**Answer format**: multiple-choice  
**Answer** **Values**: Yes/No  
**Required**: Yes

![](/assets/images/image-13.png)

**Question 2**: How long should the Temporary Access Pass be valid?  
**Answer format**: multiple-choice  
**Answer** **Values**: 60/240/480  
**Required**: Yes

![](/assets/images/image-14.png)

**Question 3**: The temporary code is delivered by SMS. What phone number should it be sent to?  
**Answer format**: Short text  
**Regex Pattern**: ^+?\[1-9\]\\d{1,14}$  
**Required**: Yes

![](/assets/images/image-15.png)

## Adjust the Logic App

The answers will be sent to the Logic App, so we must extract them from the trigger. The answers will be presented in an array.

![](/assets/images/image-17.png)

To compose our answers, I will create several expressions:

| **TAPisMultiUse** | if(equals(Triggerbody()\['Answers'\]?\[0\].Value,'Yes'),true,false) |
| --- | --- |
| **TAPLifeTimeinMinutes** | Triggerbody()\['Answers'\]?\[1\].Value |
| **Compose LifeTime Friendly Name** | Triggerbody()\['Answers'\]?\[1\].DisplayValue |
| **PhoneNumber** | Triggerbody()\['Answers'\]?\[2\].Value |

![](/assets/images/image-18.png)

Now we can simply replace the static values with our dynamic values.

![](/assets/images/image-20.png)

After replacing the static values, it should look like this:

![](/assets/images/image-21.png)

The same goes for the phone number:

![](/assets/images/image-22.png)

## End-user experience

From the managers' perspective, requesting a Temporary Access Pass will look like this:

The direct report will receive an SMS with the new TAP, including a friendly name for the TAP's lifetime.

![](/assets/images/image-24-506x1024.png)

## Let's wrap up

The more you "play" with Microsoft Entra ID Governance, the cooler it gets. The integration with Logic Apps is very powerful and brings endless possibilities. As you can see, the combination with the Requestor questions can be used to customize the experience.

Do you have more use cases like this? Let me know! The sky is the limit. ????

Stay safe.
