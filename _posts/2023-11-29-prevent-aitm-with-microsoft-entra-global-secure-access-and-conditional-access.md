---
title: "Prevent AiTM with Microsoft Entra Global Secure Access and Conditional Access"
date: 2023-11-29
categories: 
  - "entra"
  - "security"
image: "/assets/images/Secure-Your-Network-with-Entra.jpeg"
---

Microsoft Entra Global Secure Access brings a new control to Conditional Access. By installing the Global Secure Access Client on (hybrid) Entra joined devices and enabling Global Secure Access signaling for Conditional Access, admins can now work with a new condition: **_All Compliant Network locations (Preview)_**

That means we can add another layer to our tenant to prevent token theft and replay. Let's have a first look.

![](/assets/images/image-27.png)

## Prepare the lab

The first step is to activate Global Secure Access in your tenant.

![](/assets/images/msedge_bCyvUtrL37.png)

Next, we need to enable Global Secure Access signaling for Conditional Access. Using the Entra admin center, browse to **_Global Secure Access (Preview)_** > **_Global settings_** > **_Session management Adaptive access_**.  
Select the toggle to Enable Global Secure Access signaling in Conditional Access.

![](/assets/images/image-24.png)

In the Entra admin center, browse to **_Protection_** > **_Conditional Access_** > **_Named locations_**.

Confirm you have a location called **_All Compliant Network locations_** with location type **_Network Access_**. Organizations can optionally mark this location as trusted.

You can also check if the new condition is created as a Named Location using [Graph Explorer](https://aka.ms/ge).

```
GET https://graph.microsoft.com/beta/identity/conditionalAccess/namedLocations

```

![](/assets/images/image-25.png)

Next, let's create a new Conditional Access policy to require a compliant network for Office 365. Note that during the preview, you cannot use all apps. In this demo, I picked **Office 365**, but you can also select Exchange Online or SharePoint.

![](/assets/images/image-26.png)

**Scope the policy to a test user/group. Don't lock yourself out!!!!**

![](/assets/images/msedge_FHO8Qu53wD.png)

Include all locations, and exclude **_All Compliant Network locations_**. Under access controls, select **_Block Access_**.

![](/assets/images/image-15.png)

For a more detailed tutorial, check this post on Microsoft Learn: [Enable compliant network check with Conditional Access | Microsoft Learn](https://learn.microsoft.com/en-us/entra/global-secure-access/how-to-compliant-network#protect-exchange-and-sharepoint-online-behind-the-compliant-network)

Next, I installed the [Global Secure Access Client](https://learn.microsoft.com/en-us/entra/global-secure-access/how-to-install-windows-client) on a Microsoft Entra joined device. As this requires local admin rights, I used Microsoft Intune to push the installation package. That worked like a charm.

![](/assets/images/image-9.png)

After the client is installed, the user is prompted to sign in.

![](/assets/images/vmconnect_EwXDFVu8mM.png)

If we run the Client Checker tool, everything looks (almost) good.

![](/assets/images/vmconnect_hp6VbBZWl3.png)

We can also check if the Conditional Access policy works as expected. With the GSA client running, access to Microsoft 365 is granted, but when the connection is paused, the access is blocked.

##   
Secure Global Access v.s. Evilginx

Okay, now we have the setup in place, let's try to steal Debra's token. For this attack, I used [Evilginx](https://janbakker.tech/evilginx-resources-for-microsoft-365/). Debra clicks the lure and enters her credentials, but access is blocked due to Conditional Access and Global Secure Access.

Evilginx was able to steal the password but not the cookies/token.

![](/assets/images/image-28.png)

We can also check the Entra ID logs to confirm the session was blocked.

![](/assets/images/image-29.png)

## Replay the token

If you manage to steal the token otherwise, the token replay is also blocked by Conditional Access.

As expected, Conditional Access kicks in as this session does not come from a compliant network.

![](/assets/images/image-21.png)

![](/assets/images/msedge_mldoTx9Z3N.png)

## First thoughts

In this example, we used the resource-based approach with Conditional Access, but do know that Global Secure Access will also bring Universal Conditional Access to the table. [Learn about Universal Conditional Access through Global Secure Access | Microsoft Learn](https://learn.microsoft.com/en-us/entra/global-secure-access/concept-universal-conditional-access). Policies can be applied to network traffic, not just cloud applications. Exciting stuff.

Being able to use a compliant network as a condition will add another layer to the defense line. Together with the other layers, this will definitely reduce the attack factor for AiTM attacks.

1. **Require compliant device**. Devices not (hybrid) Entra joined, or compliant with Intune, will be blocked.

3. **Require a compliant network**. This is the policy we just configured. Will prevent token theft and replay.

5. **Require MFA**. Baseline protection for all apps.

7. **Require phishing-resistant authentication**. Will enforce passkeys (FIDO2 and Windows Hello) or certificate-based authentication.

![](/assets/images/image-23.png)

Learn more:

[What is Global Secure Access (preview)? | Microsoft Learn](https://learn.microsoft.com/en-us/entra/global-secure-access/overview-what-is-global-secure-access)  
[Learn about Universal Conditional Access through Global Secure Access | Microsoft Learn](https://learn.microsoft.com/en-us/entra/global-secure-access/concept-universal-conditional-access)  
[The Global Secure Access Client for Windows (preview) | Microsoft Learn](https://learn.microsoft.com/en-us/entra/global-secure-access/how-to-install-windows-client)  
[Global Secure Access (preview) traffic forwarding profiles | Microsoft Learn](https://learn.microsoft.com/en-us/entra/global-secure-access/concept-traffic-forwarding)

Stay safe!
