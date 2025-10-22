---
title: "Step-up authentication with Defender for Cloud Apps and Authentication Context"
date: 2023-06-07
categories: 
  - "cloud-app-security"
  - "entra"
  - "security"
image: "/assets/images/pexels-pixabay-417014-scaled.jpg"
---

In this post, I will show you how you can integrate Azure AD's Authentication Context with Defender for Cloud Apps to require step-up authentication for specific scenarios. Step-up authentication allows you to re-evaluate Azure AD Conditional Access policies when users take sensitive actions during a session.

To demonstrate this feature, we need to do the following steps:

![](/assets/images/image-17.png)

1. Create a (new) Authentication Context, aka "the tag".

3. Forward (all) traffic to Defender for Cloud Apps using Conditional Access App Control.

5. Create a session policy in Defender for Cloud apps, and assign the Authentication Context (tag) to the policy.

7. Create a Conditional Access policy to define what controls need to be in place for this Authentication Context.

## 1\. Create Authentication Context (tag)

First, we'll need to define the Authentication Context̀. This will be used to 'tag' resources or actions. Go to the [Authentication Context blade](https://portal.azure.com/#view/Microsoft_AAD_ConditionalAccess/ConditionalAccessBlade/~/AuthenticationContext) in the Azure Portal, and create a new one.

![](/assets/images/image-18.png)

## 2\. Forward the traffic to Defender for Cloud Apps

Before we can control the session, we need to 'proxy' the specific application to Defender for Cloud Apps. In this demo, we use SharePoint Online. Let's create a new policy to forward the traffic.

Create a new policy, and select your test user and application. For me, I selected Nestor Wilke and SharePoint.

![](/assets/images/image-29.png)

Since session policies only apply to browser sessions, there is no need to forward desktop and mobile sessions.

![](/assets/images/image-31.png)

On the session blade, select "_Use Conditional Access App Control_" and "_Use custom policy_".

![](/assets/images/image-30.png)

## 3\. Create the session policy in Defender for Cloud Apps.

Now that the browser traffic is sent to Defender for Cloud Apps and the Authentication Context is in place, we can create the session policy in MDfCA. Head over to the Microsoft 365 Defender portal, and scroll down to Cloud Apps. Create a new session policy.

![](/assets/images/image-33.png)

Don't use a template for now, and pick the "Block activities" control type. Then, select OneDrive and SharePoint and the activity types of your choice, and make sure the policy will only apply to Nestor, our test user.  
  
If your app is not listed, please ensure it is onboarded first. [Deploy Conditional Access App Control for catalog apps with Azure AD - Microsoft Defender for Cloud Apps | Microsoft Learn](https://learn.microsoft.com/en-us/defender-cloud-apps/proxy-deployment-aad)

![](/assets/images/image-34.png)

We don't configure content inspection for now, but this can also be applied if needed. In the action section, select "Require step-up authentication" and pick the Authentication Context 'tag' that's been created earlier.

![](/assets/images/image-35.png)

## 4\. Create a Conditional Access policy

In the last step, we will define what controls apply to the session when the specific action is executed and the authentication context is triggered. This will be configured in a conditional access policy. Create a new policy, and select the Authentication context on the Cloud apps or actions pane.

![](/assets/images/image-19.png)

Then, configure the desired access controls. In this case, I want the user to do Azure MFA, and consent to a terms of use.

![](/assets/images/image-20.png)

## User experience

Now, let's see how this looks for Nestor. When Nestor wants to access a file on OneDrive or SharePoint, the sessions is being redirected to Defender for Cloud apps. Also, the session policy kicks in.

![](/assets/images/image-24.png)

Now, Nestor wants to copy, paste, or print the file, and is prompted to do a security check, basically the step-up authentication.

![](/assets/images/image-25.png)

Nestor is now redirected, and the corresponding conditional access policies are re-assessed. First, Nestor is prompted for Azure MFA.

![](/assets/images/image-26.png)

Next, the term of use is prompted.

![](/assets/images/image-27-1024x640.png)

After both actions are performed, Nestor is now redirected back to the file where he can continue to work.

![](/assets/images/image-28.png)

## Wrap things up

Defender for Cloups apps is a great tool for many use cases. In most cases, MDfA is a good starting point for BYOD access, guest access, or session control for high-risk or privileged sessions. Now with the step-up authentication feature, this brings in another layer of security. This example was just to show what you can do, but since it will use Conditional Access (Authentication Context) the sky is the limit.

Stay safe!
