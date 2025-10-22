---
title: "Prepopulate phone methods for MFA and SSPR using Graph API"
date: 2020-07-30
categories: 
  - "entra"
  - "logic-apps"
  - "power-platform"
  - "security"
tags: 
  - "graph-api"
  - "graph-explorer"
  - "mfa"
  - "power-automate"
  - "sspr"
image: "/assets/images/vintage-technology-calling-numbers-105003.jpg"
---

* * *

## Part 1 - Graph API

What is the number one task if we want to protect our identity? Right. **Turn on MFA.**  
What is the number one task our helpdesk is busy with all day (and night)? Right. **Password Resets.**  
What is the number one struggle when we want to implement security? Right. **User experience.**  
So, what are you gonna get by reading this blog post? **Some light at the end of the tunnel.** Buckle up!

## What's the case here?

I start at the bottom. Because I know there are still a lot of organizations that don't have MFA enabled for their users. So, in case you have not yet implemented MFA, you can now prepopulate your users' phone numbers. This is the most basic security layer that you need to have in place right now. Your users don't have to register first. Just provision the phone number and turn on MFA. So that's use case 1.

But what if you want to take it a step further? Say that your company just purchased a bunch of FIDO2 security keys to hand out to all users? In that case, you need to have a least one MFA method already registered, before you can start using the FIDO2 key. So there is use case 2.

I bet you have a lot more use cases where you just want to pre-provision the user's mobile phone or office number to use for multi-factor authentication or self-service password reset. Think about your onboarding process. Isn't it cool to hand out basic protected accounts to your new users, students, or customers? That is now possible with Microsoft Graph API. And when I say **_API_**, the sky is the limit.

## phoneAuthenticationMethod API (beta)

So, let start with a warning. This [API](https://docs.microsoft.com/en-us/graph/api/resources/phoneauthenticationmethod?view=graph-rest-beta) is still in beta. So when you are using this in a production environment, this is not supported. I might work perfectly, but be aware of this.

I've created this overview of the capabilities of this API. To understand how this API works, you have to know that there are three types of phone authentication methods that you can set, update, or delete:

- Mobile (_3179e48a-750b-4051-897c-87b9720928f7_)
- Alternate mobile _(b6332ec1-7057-4abe-9331-3d72feddfe41)_
- Office phone _(e37fc753-ff3b-4958-9484-eaa9425c82bc)_

![](/assets/images/Azure-AD-Authentication-methods.png)

Here's an example of a query for all phone methods:

![](/assets/images/image-25.png)

1. Each phone method is specified by an ID. The ID global, so it's the same for all users.
2. The phone number to text or call for authentication. Phone numbers use the format "+ x", with extension optional. For example, +1 5555551234 or +1 5555551234x123 are valid. Numbers are rejected when creating/updating if they do not match the required format.
3. The phone type (mobile, alternateMobile, or office)
4. The SMS sign-in state property gives information about whether or not a phone number is ready to sign in via SMS. The following are the possible values: [not supported, notAllowedByPolicy, notConfigured, or ready.](https://docs.microsoft.com/en-us/graph/api/resources/phoneauthenticationmethod?view=graph-rest-beta)
5. You can enable [SMS Sign-in](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-authentication-sms-signin) on the mobile phone type only. Also, the phone number **_must be unique_** in the tenant.

```
NOTE: You cannot add an alternate mobile before adding the mobile number. 
```

You can also find (and change) these values in the Azure Active Directory admin portal.

![](/assets/images/image-29.png)

_The Office phone is located on the Profile page, but be aware that, if the user is synced from your on-premises Active Directory, this value cannot be changed from the cloud._

## Permissions

In delegated scenario's, the users needs to have on of the following permissions:

- Global admin
- Global reader
- Privileged authentication admin
- Authentication admin (only sees masked phone numbers)

When you use the Graph API, you'll need either _UserAuthenticationMethod.Read.All_, or _UserAuthenticationMethod.ReadWrite.All_

## Using the Graph Explorer

You can use the [Graph Explorer](https://aka.ms/ge) to figure out what the API is capable of, and what the responses are for the requests. To get you started, here are some examples that you can use:

```
GET https://graph.microsoft.com/beta/users/{id}/authentication/phoneMethods/
GET https://graph.microsoft.com/beta/users/{id}/authentication/phoneMethods/{phonemethodID} 
```

![](/assets/images/image-26.png)

Replace the _{id}_ with the Object ID of the Azure AD user and _{phonemethodID}_ with either **3179e48a-750b-4051-897c-87b9720928f7** (mobile), **b6332ec1-7057-4abe-9331-3d72feddfe41** (alternate mobile}, or **e37fc753-ff3b-4958-9484-eaa9425c82bc** (office phone)

To add a phone number, use this query:

```
POST https://graph.microsoft.com/beta/users/{id}/authentication/phoneMethods
```

Use this as the body to provide the phone number and phone type:

```
{
  "phoneNumber": "+1 2065555555",
  "phoneType": "mobile"
}
```

![](/assets/images/image-27.png)

You can use the same body to update the mobile phone number. Use this query:

```
PUT https://graph.microsoft.com/beta/users/{ID}/authentication/phoneMethods/3179e48a-750b-4051-897c-87b9720928f7
```

Also take a look at [this blog post](https://identity-man.eu/2020/07/08/pre-configure-authentication-methods-for-end-users-in-azure-ad/) from Pim Jacobs that shows how you can use PowerShell and Graph API together to pre-configure the authentication methods.

## What's next?

As you can imagine, the sky is the limit here. Once you understand the API, you can build awesome automation solutions with it.

![](/assets/images/432-29-07-2020.png)

In my next blog, I will show you how you can use Power Automate to provision the phone numbers to Azure AD using an Excel file on SharePoint/Onedrive. I wrapped the available APIs into a **_custom connector_** to make it really easy to use. You can simply pick up the connector from [Github](https://github.com/BakkerJan/Power-Automate/tree/master/Azure%20AD%20authentication%20methods%20-%20Custom%20Connector) and upload it to your environment. Just hook up the connector to your Azure AD for authentication and permissions, and you are ready to go.

![](/assets/images/Prepopulate-phone-number-for-MFA-and-SSPR-1.png)

[Part 2 - Prepopulate phone methods using a Custom Connector in Power Automate - _Populate phone numbers to Azure AD using Power Automate and a custom connector_](https://janbakker.tech/prepopulate-phone-methods-using-a-custom-connector-in-power-automate/)
